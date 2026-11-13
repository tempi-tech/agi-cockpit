<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Home and memory

Learn which files are in a Crew's home folder, who changes what, what happens when something is deleted, and how to ask a Crew to remember things.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-home)

Each Crew has its own home folder. Its role, memory, work records, and Missions are kept there. Even after its session starts over, the Crew reads its home and continues.

To open the home, choose **Open home folder** in the Crew's row menu, or **Open folder** under **Home** in the Crew's settings. The home is on the computer running Cockpit.

## What is in the home

| Location | Contents | Who changes it | Git history |
| --- | --- | --- | --- |
| `AGENTS.md` | Role and rules (up to 4,000 characters) | The owner in the app. The Crew when asked | Yes |
| `HEARTBEAT.md` | What to check during periodic checks | The owner or the Crew | Yes |
| `memory/` | What it has learned (decisions, feedback, preferences, deadlines) | Mainly the Crew | The Crew commits |
| `.agents/skills/` | The Crew's skills | The owner or the Crew | The Crew commits |
| `missions/` | Each Mission's goal, stages, and decisions, plus a log of activity | Mainly the Crew | The Crew commits `README.md` |
| `work/` | Work and records for each piece of work | Mainly the Crew and child tasks | No |
| `archive/` | Finished work | Mainly the Crew | No |

Besides these files, Cockpit gives the Crew its "Crew standard" in every session. It is the foundation for how the Crew talks and when it confirms things. The owner cannot change it.

## AGENTS.md

`AGENTS.md` holds the Crew's role and rules. At first it has four headings: role, how to reply, what it may do on its own, and actions that need confirmation.

**There are two ways to change it.**

- **Change it by talking.** Ask, for example, "Add to your role that weekly reports are written in English." For a clearly requested change, the Crew rewrites it as asked. When it wants to change something on its own judgment, it shows the diff and the reason and asks for your agreement
- **Change it in the app.** Edit **Instructions (AGENTS.md)** in the Crew's settings and press **Save**. After saving, **Saved and committed** appears

When the Crew changes it, **The Crew changed its AGENTS.md** appears in the conversation.

If the Crew changes the same file while you are editing it, the app shows the current content. Choose **Restore my changes** or **Discard my changes**.

Content over 4,000 characters cannot be saved. Ask the Crew to move long procedures into a skill or memory.

### Preventing changes requested by people who may talk to it

`AGENTS.md` and the Crew's settings also change on requests from people who may talk to it. To limit this to the owner, set **Changes asked for in chat** in the Crew's settings to **Only the owner**. The default is **The owner and allowed people**.

Whichever you choose, changes that loosen the approval mode or greatly widen who may talk to it always wait for the owner's approval in Cockpit.

## Memory

The Crew writes feedback, preferences, and decisions to `memory/` as soon as it receives them. You can also ask:

- "Remember this: invoices go out at the end of every month"
- "Cancel the deadline I mentioned before. Fix your memory too"
- "Tell me what you remember about me"

`memory/MEMORY.md` is the index, and each memory is one file. Memories with a time limit get an expiry date, and the Crew clears them once it passes. At startup the Crew reads only the index and opens the contents when needed.

**Memory is not permission.** Even if a memory says "this was allowed before," that does not permit the current action.

**Memory is not separated by person.** Something learned from a request by one person who may talk to it can be used for another person's request. Do not have it remember secrets. The Crew is instructed to hide secrets in its memory and work results, but this is not guaranteed either.

## Work records

The Crew creates `work/<work>/README.md` for each piece of work. It writes who asked, the goal, the status, the next step, replies it is waiting for, and promises, and updates them as the work moves forward.

The Crew treats work as done when it has returned the result to the person who asked. Just making something or just checking it is not done.

The Crew moves finished work to `archive/`. **The Crew does not delete work. The owner does the deleting.**

## Mission records

When you give the Crew a Mission, it puts `README.md` and `log.md` in `missions/<mission>/`.

- `README.md`: the goal, the criteria for achieving it, the stages, what it may do without asking, and decisions
- `log.md`: a record of decisions, actions, and findings. It is only ever added to

The list of Missions (title, status, due date, now, next step, and so on) is kept inside Cockpit, not in the home. Even if files in the home break, the list does not. You can see its contents in the **Missions** tab of the Crew's settings. For details, see [Missions](https://agi-labo.com/en/tools/cockpit/docs/crew-missions).

## What happens when something is deleted

| Deleted item | What happens |
| --- | --- |
| `AGENTS.md` | When Cockpit tidies up the home, it restores the last version from Git. If it cannot, it creates the initial template |
| `HEARTBEAT.md`, `memory/MEMORY.md` | When Cockpit tidies up the home, it creates the initial template |
| Individual memories in `memory/`, files in `missions/` | Can be restored if they remain in the Git history |
| `work/`, `archive/` | Not in Git, so Git cannot restore them |
| The Crew | Deletes its settings and its list of Missions, and moves the home to the Trash. If the home cannot be moved, Cockpit tells you where it is. **For a Crew created from a task, the original task is deleted too** |

## Having it work in a project

At first, the Crew works in its home. You can choose a project under **Works in** in the Crew's settings, or with **Change project…** in the row menu.

- The Crew works in the project's folder
- The home remains the place for `AGENTS.md` and memory, and is given to the Crew as an extra folder
- Child tasks belong to that project
- Changing it starts a new session. The conversation carries over

If you set the place it works to somewhere inside a Crew's home, Cockpit warns you, because it would mix with other Crews' instructions and memory. **Move back to its home** returns it to its home.

**The home is not a sandbox.** There is no guarantee that the Crew will not touch files outside its home. For details, see [Safety and treating people differently](https://agi-labo.com/en/tools/cockpit/docs/crew-safety).

## Related pages

- [How a Crew works](https://agi-labo.com/en/tools/cockpit/docs/crew-work)
- [Periodic checks and Autoruns](https://agi-labo.com/en/tools/cockpit/docs/crew-schedule)
- [Missions](https://agi-labo.com/en/tools/cockpit/docs/crew-missions)
