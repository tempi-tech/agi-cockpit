<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Missions

Learn how to give a Crew a goal that takes weeks or months as a Mission, how to follow its progress and what needs you in the Missions tab, and how to pause or end it.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-missions)

A Mission is a long goal you give to a Crew. When you hand it work that takes weeks or even years, the Crew moves it forward one step at a time at each periodic check. You can see the progress, and what needs the owner's decision, in the **Missions** tab of the Crew's settings.

For a request that is done in one go, just ask in chat as usual. For work at set times, use an Autorun. For when to use which, see [Periodic checks and Autoruns](https://agi-labo.com/en/tools/cockpit/docs/crew-schedule).

## Two types

| Type | Good for | How it ends |
| --- | --- | --- |
| **Goal** | Goals with criteria for achieving them, such as "Publish the new help pages by the end of March" | It ends when the criteria are met. You can give it a due date |
| **Ongoing** | Work with no set end, such as "Track trends in inquiries every week" | It continues until the owner ends it. It has no due date |

The Crew suggests the type based on your answers to its questions.

## Giving a Mission

There are three ways to ask. Each starts the same way.

- **Ask in chat.** "I want to give you a new Mission. I want to publish the new help pages by the end of March"
- **Ask in Cockpit.** Write the same thing in the message box of the Crew's conversation
- **Ask from the Missions tab.** Press **New Mission**, write in the **What should it take on? A few words are enough** field, and press **Tell the Crew**. **Told (Crew name). It will ask a few short questions in its conversation.** appears

### Answering the first questions

When you ask, the Crew first asks 3 to 4 short questions together in one Ask. You can answer by choosing from options.

- What to aim for
- Whether there is a due date
- What it may do without asking
- Whether to review at set times (check-ins)

From your answers, the Crew puts together a one-sentence goal, the type, the due date, the stages, the first step, and what it may do on its own, and creates the Mission. It appears in the **Missions** tab.

**Only the owner's requests can create a Mission.** When someone other than the owner who may talk to it asks, the Mission waits for the owner's approval in Cockpit. It is created once approved.

### Where check-ins are placed

Where check-ins are placed depends on whether you are an AGI Lab member.

| | Member | Not a member |
| --- | --- | --- |
| Where | An Autorun for the Mission | `HEARTBEAT.md` in the home |
| Time | The time you set | An approximate time, at a periodic check |
| In the tab | Appears under **Scheduled** | Does not appear |

## Reading the Missions tab

The **Missions** tab lists Missions that have not ended, most urgent first. Each row shows the title and these labels.

| Label | Meaning |
| --- | --- |
| **Goal**, **Ongoing** | Type |
| **Active**, **Paused**, **Achieved**, **Cancelled** | Status |
| **Due (date)** | Due date |
| **Needs you: (number)** | How many things need the owner's decision or action |
| **No movement for (number) days** | Days since it last moved |

Ended Missions are grouped under **Ended (count)** at the bottom.

Pressing a row opens the contents of that Mission.

| Heading | Contents |
| --- | --- |
| **Where it stands** | **Now**, **Next step**, **Waiting for** |
| **Needs you** | Asks waiting for an answer, and tasks or Autoruns waiting for approval or stopped |
| **Scheduled** | Autoruns for the Mission and the **Next** time |
| **Recent activity** | Child tasks, Autoruns, Fleets, and Asks for the Mission, and the Crew's **Log** |
| **README** | The goal, the criteria for achieving it, the stages, what it may do on its own, and decisions |

**Needs you** and **Scheduled** appear only when they have something in them. **Back to Missions** returns you to the list.

## Answering what needs you

Within what you entrusted to it, the Crew goes ahead without asking. It asks through an Ask only about decisions outside that. A Mission's Asks appear under **Needs you**, and you can answer them inside the tab. Like other Asks, you can also answer them in the Ask window or, if set up, in a Discord, Slack, or Chatwork DM.

The Crew adds your answer to the decisions in the README and does not ask about the same kind of thing again.

**Needs you** also shows a child task for the Mission that is waiting for approval or has stopped. **Open** takes you to that task.

## Pausing and ending

These buttons are at the top of a Mission's contents.

| Button | What it does |
| --- | --- |
| **Pause** | The Crew stops moving that Mission forward. Autoruns for the Mission also stop |
| **Resume** | Moves a paused Mission forward again. Stopped Autoruns come back too |
| **Mark achieved** | Ends it as achieved |
| **Mark cancelled** | Ends it as cancelled |

**Mark achieved** and **Mark cancelled** end the Mission after a confirmation. **An ended Mission cannot be resumed.** Autoruns for the Mission stop, and unanswered Asks close. To continue, ask for it as a new Mission.

**The owner decides to pause, resume, achieve, or cancel.** When the Crew decides "the criteria are met," or someone asks it in chat to "pause it," the owner receives **(Crew name)'s Mission confirmation** from the Crew. Nothing changes until the owner approves.

## Common pitfalls

- **When the periodic check is off, Missions do not move forward on their own.** The tab shows **Periodic checks are off, so (Crew name) will not advance its Missions by itself.** Use **Open Schedule** to turn the periodic check on
- **Without a membership, check-in times are approximate.** They depend on the periodic check's interval and hours
- **The list of Missions is kept inside Cockpit.** Deleting files in `missions/` in the home does not remove the list. Deleting the Crew removes the list too, and the files move to the Trash with the home
- **The Crew may not act right away.** It moves Missions forward at periodic checks. For an urgent step, ask it directly in chat

## Related pages

- [Periodic checks and Autoruns](https://agi-labo.com/en/tools/cockpit/docs/crew-schedule)
- [Asks and Crews](https://agi-labo.com/en/tools/cockpit/docs/crew-ask)
- [Home and memory](https://agi-labo.com/en/tools/cockpit/docs/crew-home)
