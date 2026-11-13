<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Periodic checks and Autoruns

Learn how to set up the periodic check in which a Crew reviews its own work, how to use Autoruns to give a Crew work at set times, and when to use a Mission instead.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-schedule)

There are two ways to make a Crew act on a schedule. Both are in the **Schedule** tab of the Crew's settings.

| | Periodic check | Autorun |
| --- | --- | --- |
| What it does | The Crew reviews its own work | Sends a set instruction at a set time |
| Good for | Picking up child task results, checking when promises are due, moving Missions forward | A morning summary every day, a message tomorrow at 10:00 |
| Timing accuracy | Not exact. Some checks are pushed back and some are skipped | Sends at the time you set |
| Requirements | None | AGI Lab membership |

**Both work only while the computer and Cockpit are running.**

**Hours** in the **Schedule** tab is when periodic checks run. When the Crew responds to chats from people other than the owner is set separately, in **Active hours**. For details, see [People and places](https://agi-labo.com/en/tools/cockpit/docs/crew-people).

## Periodic check

The periodic check is on from the start. It wakes the Crew every 30 minutes between 08:00 and 23:00. Times are those of the computer running Cockpit.

You can change it under **Periodic check** in the **Schedule** tab.

| Item | What you can choose |
| --- | --- |
| **On** | On or off |
| **Interval** | 30 to 240 minutes, in steps of 30 minutes |
| **Hours** | **Always** or **Between set times**. Ranges can cross midnight, such as 22:00–06:00 |
| **Skip checks when there is nothing to check** | On by default |

You can also change it by asking in chat, for example, "Make the periodic check every hour."

### Counted from the last conversation

The periodic check is pushed back until the **Interval** has passed since the last conversation. A conversation means a message arriving, the Crew finishing a response, or the Crew posting in the chat. While the conversation continues, the count restarts each time.

For example, with a 30-minute interval, if a message arrives at 10:20 and the Crew finishes replying at 10:22, the next periodic check is at 10:52 or later. Child task notices and progress notices do not restart the count.

### What it checks

At every periodic check, the Crew checks child tasks, Asks, the Queue, Autoruns, work it has not returned yet, expired memories, and active Missions. It also looks at the items written in `HEARTBEAT.md` in its home.

You can change `HEARTBEAT.md` by talking:

- "At every periodic check, look for GitHub reviews nobody has responded to"

Tips for writing it:

- **Keep it short.** It is read every time, so the longer it is, the more each check costs
- **Be specific.** An item like "see if anything is wrong" makes the Crew look for work that does not exist
- **Do not put work for set times in it.** Use an Autorun instead

### Seeing whether it ran

The result appears as one line in the Crew's conversation.

| Display | Meaning |
| --- | --- |
| **Periodic check (time) · All clear** | It checked and changed nothing |
| **Periodic check (time) · Nothing to check (skipped)** | The Crew had declared nothing needed it, so it was not woken |
| **Periodic check (time)** with the Crew's report | The Crew did something |

Consecutive checks are grouped, for example **Periodic checks 08:00–10:00 · All clear (5)**.

In the **Schedule** tab, you can see the result of the **Last** check and the time of the **Next** one.

### Skipping by declaring nothing needs it

When the Crew checks and decides that "nothing needs me until the next time," it declares so. If **Skip checks when there is nothing to check** is on, checks where nothing has changed since then do not wake it. The **Schedule** tab shows **Declared nothing to check at (time)**.

The declaration is cleared and the Crew is woken again when any of these happens:

- A new message or notice arrives
- A file in the home changes
- The owner changes or pauses a Mission
- The time the Crew gave in its declaration arrives
- 6 hours have passed since the declaration

### Skipped checks

A check is skipped in the following cases. Skipped checks are not run later in a batch.

- Stopped, or restarting
- Working, or waiting for approval
- Messages waiting
- Usage limit, or waiting to retry delivery
- Stopped by the memory guard
- Outside the hours

The **Last** entry in the **Schedule** tab shows the reason, such as **Skipped (working)**. When the computer wakes from sleep, at most one of the missed checks runs.

**Periodic checks also use your AI usage allowance.** Lengthening the interval or narrowing the hours reduces it.

### When the Crew contacts people

During a periodic check, the Crew contacts people only when it needs their decision, when a promised time has come, or to report a failure. It does not send "all clear."

## Autorun

Autorun is for AGI Lab members.

There are two ways to create one.

- **Ask by talking.** "On weekday mornings at 9:00, summarize today's schedule and post it here"
- **Create it in the app.** Press **Add an Autorun** in the **Schedule** tab. Enter a name and an instruction, and choose **Run timing** from **Once**, **Interval**, or **Scheduled**

You can also create one from **Autorun tasks** above the task list by choosing **Crew** under **Execution target**. For details, see [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun).

### One-time promises

For a promise like "Let me know tomorrow at 10:00," the Crew creates a **Once** Autorun addressed to itself. Relying on periodic checks would make the time drift.

**Without a membership, this cannot be created.** In that case, the Crew puts the promise in its work record and checks it during periodic checks. The time becomes approximate, and the Crew says so. Do not leave promises where the time matters entirely to the Crew.

### Clearing finished one-time schedules

One-time schedules that have already run are grouped under **Finished (count)** at the bottom of the **Schedule** tab. Open it to see each name, the time it ran, and any failed runs.

Pressing **Delete all** asks for confirmation, then deletes only that Crew's finished one-time schedules. Upcoming, paused, and recurring schedules are not deleted.

### Common pitfalls

- **Instruction delivered does not mean the work is done.** It means the instruction reached the Crew's Queue. Check the result in the Crew's conversation
- **While the Crew is stopped, the instruction waits in the Queue.** It arrives when the Crew starts
- **The Crew's settings at the time of delivery apply.** This includes the approval mode
- **When a Crew is deleted, Autoruns addressed to it are disabled and show Needs attention.** Choose another destination before enabling them again
- **Autoruns created by the Crew are not treated as the owner's instructions.** The Crew's own limits apply to tasks from Autoruns it created. Autoruns the owner created are treated as the owner's requests
- During a periodic check, the Crew does not create new recurring schedules

If Cockpit was not running at a scheduled time, the schedule runs once if Cockpit starts within 24 hours of that time.

## When to use a Mission instead

Give a goal that takes weeks or months as a Mission. The Crew moves a Mission forward one step at a time at each periodic check. When a review at set times (a check-in) is needed and you are a member, the Crew creates an Autorun for the Mission.

| What you want | What to use |
| --- | --- |
| Have it do set work at set times | Autorun |
| Have it reliably follow up on work you asked for | Periodic check |
| Give it a long goal and watch the progress | Mission |

**When the periodic check is off, Missions do not move forward on their own.** For details, see [Missions](https://agi-labo.com/en/tools/cockpit/docs/crew-missions).

## Related pages

- [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun)
- [Missions](https://agi-labo.com/en/tools/cockpit/docs/crew-missions)
- [Home and memory](https://agi-labo.com/en/tools/cockpit/docs/crew-home)
- [How a Crew works](https://agi-labo.com/en/tools/cockpit/docs/crew-work)
