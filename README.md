# understand-git

Forces you to understand the code you let an AI write.

Pick a commit, and the app shows you every diff block in it. Under each block you type what the change does. Claude Code grades your answer, and the commit only counts as understood once every block has a correct answer.

Grading looks at the overall logic and configuration, not technical detail. Imprecise wording is fine; an answer vague enough to fit any change ("updates the code") is not.

## Requirements

- [Node.js](https://nodejs.org) 18 or newer
- [git](https://git-scm.com)
- The [Claude Code CLI](https://claude.com/claude-code), logged in (run `claude` once and use `/login`)

There are no npm dependencies to install.

## Run

```bash
npm start
```

Then open http://localhost:4477. Keep the terminal open; the page stops working when the server stops.

## How it works

1. **Choose a folder.** Type a path or use *Browse…*. Any folder inside a git repository works. The current branch and its newest commit are selected automatically.
2. **Explain each block.** Every diff hunk is shown with its file path above and a textbox below. Type what the change does and press Enter (Shift+Enter for a new line).
3. **Get graded.** Claude Code evaluates the answer.
   - Wrong: the textbox gets a red border, and an *Explain it to me* button appears.
   - The button shows Claude's explanation of the block. You still have to type an answer yourself and have it pass.
   - Correct: the textbox gets a green border and locks.
4. **Finish.** When every block is correct, confetti.

Details worth knowing:

- Pasting into the answer box is disabled, and the explanation text cannot be selected. The answer has to be typed.
- Lockfiles (such as `package-lock.json`), binary files and pure renames are not quizzed. They are listed at the bottom of the page.
- Merge commits are compared against their first parent.
- Correct answers and the last folder are remembered in the browser, so a reload keeps your progress.
- The commit list shows the newest 200 commits of the selected branch.

## Configuration

Set these environment variables before starting the server.

| Variable | Default | Purpose |
|---|---|---|
| `PORT` | `4477` | Port the server listens on |
| `CLAUDE_BIN` | `claude` | Command used to run Claude Code |
| `CLAUDE_MODEL` | `sonnet` | Model used for grading and explanations |

The server looks for `claude` on your `PATH` and, as a fallback, in `~/.local/bin`.

## Troubleshooting

| Message | Fix |
|---|---|
| Cannot reach the understand-git server | The server is not running. Start it with `npm start`. |
| Not logged in · Please run /login | Run `claude` in a terminal and log in with `/login`. |
| 'claude' is not recognized | Install the Claude Code CLI, or point `CLAUDE_BIN` at it. |

## Privacy and security

- The server only listens on `127.0.0.1` and rejects requests not addressed to localhost.
- For each answer, the diff block, the other changes in the same file, the commit message and your answer are sent to Claude through your Claude Code login.
- Each grading call uses your Claude Code usage.

## Project layout

| File | Contents |
|---|---|
| `server.js` | HTTP server, git access, diff parsing, Claude Code calls |
| `public/index.html` | The whole UI: markup, styles and script |
