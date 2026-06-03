# Session Ledger

This is the active ACP Session Ledger for session `S005`.
It is **agent-readable work memory**: a place where the user keeps
notes (observations / context) and tasks (trackable work items),
and where agents can be asked to review, plan, and recommend — but
**not** mutate.

## Layout

```
.agent/sessions/S005/ledger/
├── README.md         this file — read first
├── notes/            markdown bodies, one file per record
├── notes.jsonl       SOURCE OF TRUTH for every record (notes + tasks)
└── tasks.jsonl       READ-ONLY derived view filtered from notes.jsonl
                      where kind == "task"
```

## Notes vs tasks

- **Notes** are human observations / context. They have a markdown body
  in `notes/<id>_<slug>.md`.
- **Tasks** are trackable work items, **promoted from notes**. Same
  record shape as a note plus a `status` field. Can be demoted back to
  a plain note.

## Task statuses

| status        | meaning                                                  |
| ------------- | -------------------------------------------------------- |
| `planned`     | not started                                              |
| `in_progress` | actively being worked on                                 |
| `completed`   | done                                                     |
| `canceled`    | abandoned / no longer relevant                           |

## Task IDs

Every record promoted to a task gets a stable human-reference ID
of the form `T<6digits>` (e.g. `T482913`). Rules:

- Assigned **once** on promotion. Never regenerated.
- Survives rename, slug changes, demotion-to-note, and
  re-promotion. The same task always has the same `taskId`.
- The `displayId` an agent or human refers to is the combination:
  `T482913_fix-chat-markdown-spacing` (id + slug). Always include
  the id in references so renaming doesn't break the link.
- The underlying `id` field (`n<random>`) stays as the internal
  primary key for file paths and storage; do not use it in
  conversation.

**Agents must reference tasks by `taskId`**, not by title alone.
"Mark T482913 as in_progress" is unambiguous; "mark the markdown
spacing task" is not.

## Agent contract

**Default mode is REVIEW ONLY.** Treat the ledger as read-only unless
the user has *explicitly* asked for a mutation in this turn.

### Agents MAY

- read every file under this directory
- read `notes/*.md`, `notes.jsonl`, `tasks.jsonl`
- identify unfinished tasks
- identify notes that imply unimplemented work
- propose an implementation order
- generate small execution prompts the user can run next
- **recommend** task status changes by listing exact task IDs / titles

### Agents MUST NOT

- modify `notes.jsonl`
- modify `tasks.jsonl` — it is a derived view, never the source
- modify any `notes/*.md` body
- change a task's status
- archive, delete, rename, promote, or demote any record
- act on vague references like *"mark them"*, *"update those"*,
  *"set the selected ones"*, *"you know which ones"*

…unless the user has explicitly authorized the change in this turn
with exact task IDs or exact task titles.

### Disambiguating mutations

If the user asks for a change and the target is ambiguous:

1. Do **not** guess.
2. Return the **proposed** change as a recommendation.
3. Ask for confirmation that lists the exact task IDs or exact titles
   you would touch.

Only after the user replies with the explicit identity may a change
proceed.

### Why `tasks.jsonl` is off-limits even with permission

`tasks.jsonl` is mechanically rebuilt from `notes.jsonl` after every
write. Editing it directly will be overwritten and the mutation will
silently disappear. The **only** safe write path is to update the
source record in `notes.jsonl`. The recommended way to do that today
is via the ACP-approved update command exposed by this app — see
"ACP-approved status update path" below — not by hand-editing JSONL.

## ACP-approved status update path

```
notes_set_kind_status(noteId, { status: <new_status> })
```

This Tauri command (invoked by the UI on click / dropdown) writes
through `notes.jsonl`, bumps `updatedAt`, and rebuilds the
`tasks.jsonl` view atomically. Agents do **not** need to know about
this command unless asked to drive the UI; agents do **not** call it
directly. Prefer recommending the change and letting the user click.

## Schema reference

Each line in `notes.jsonl` / `tasks.jsonl` is a JSON object with at
least:

```
{
  "id":           "n<random>",
  "sessionId":    "<S###>",
  "title":        "<editable display title>",
  "slug":         "<filesystem-safe slug>",
  "filePath":     "<absolute path to the .md body>",
  "kind":         "note" | "task",
  "status":       "planned" | "in_progress" | "completed" | "canceled", // optional, only when kind=="task"
  "taskId":       "T<6digits>",                                          // optional, only when kind=="task"
  "sourceNoteId": "n<random>",                                           // optional, set on promotion
  "collapsed":    false,
  "archivedAt":   "<iso timestamp>",                                     // optional
  "createdAt":    "<iso timestamp>",
  "updatedAt":    "<iso timestamp>"
}
```

## How a "review" turn typically goes

1. The user sends a Review Ledger prompt (often via the UI button).
2. You read `notes.jsonl` and `tasks.jsonl` from this directory.
3. You return a short triage:
   - recommended implementation order
   - 2–3 small execution prompts the user can dispatch next
   - exact task IDs / titles you recommend moving to `in_progress`
   - exact task IDs / titles that should stay `planned`
   - unclear items that need user confirmation
4. **You stop there.** No file writes. No status changes.
5. The user either confirms with exact IDs / titles, or refines the
   plan. Only on explicit confirmation may a status change happen,
   and even then it should flow through the ACP-approved command path,
   not a direct JSONL edit.
