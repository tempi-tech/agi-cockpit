<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Create your first Crew

Start talking with the Assistant Crew from first-run setup, create a Crew from the New Task screen, decide its role, and turn a task into a Crew.

> Verified with AGI Cockpit 4.132.0 on 2026-11-13. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/crew-first)

When you finish these steps, you will have one Crew and can talk with it inside Cockpit. There are three ways to create a Crew.

| How to create it | When it suits |
| --- | --- |
| [Create an Assistant in first-run setup](#create-an-assistant-in-first-run-setup) | When you open Cockpit for the first time |
| [Create one from the New Task screen](#create-one-from-the-new-task-screen) | When you want to decide a role and create a Crew |
| [Turn a task into a Crew](#turn-a-task-into-a-crew) | When you want an ongoing task to take on a role as it is |

If you are not an AGI Lab member, you can create only 1 Crew. Stopped Crews count too. For what a Crew is, see [What is a Crew](https://agi-labo.com/en/tools/cockpit/docs/crew-about).

## Create an Assistant in first-run setup

When you open Cockpit for the first time, the **Welcome to AGI Cockpit** screen shows "First, Cockpit sets up a Crew, an AI colleague that works on your behalf, as your Assistant."

1. Press **Get started**.
2. In **Set up an agent**, sign in to the agent you will use and press **Continue**. One agent, such as Claude Code or Codex, is enough to start.
3. If you are not signed in to AGI Lab, the **Sign in with AGI Lab** screen appears. To go on without signing in, press **Start without signing in**.
4. If there is earlier agent work Cockpit can bring in, the **Continue where you left off** screen appears. To skip it, press **Skip for now**.
5. Cockpit shows **Setting up your Assistant…**, creates the Assistant Crew, and opens its conversation.

The Assistant is named "Assistant" and its role is to help with research, summaries, drafts, and notes. It runs on the agent you made available during setup. If both Claude Code and Codex are available, it uses Claude Code. For details on each screen, see [Initial setup](https://agi-labo.com/en/tools/cockpit/docs/initial-setup).

Cockpit does not create the Assistant in these cases.

- You already have a Crew
- You went ahead with **Set up later** without any available agent

If the Assistant could not be set up, **Couldn't set up your Assistant** appears with the reason. Use **Try again** to retry, or **Not now** to finish setup.

## Give the Assistant its first job

At the top of the conversation, the instructions Cockpit sent to the Assistant appear as a single **First instructions** line. Select it to read the contents.

1. The Assistant briefly introduces itself, gives three examples of what you can ask, and asks what you would like it to do. Its first reply is an AI response, so it takes from a few seconds to a few tens of seconds.
2. Write what you want in the input field and send it. The greeting and the examples are text, so you cannot select them. Quick jobs such as the example "Summarize three AI news stories from today" suit this well.
3. The Assistant does that job first and writes the result in its reply.
4. The Assistant asks once, with an Ask, about a drafted role and name that fit the job you asked for.
5. Choose **Use this role** or **Keep as Assistant**, then press **Submit**.
6. The Assistant proposes one job worth continuing.

| What you chose | What happens |
| --- | --- |
| Use this role | The Assistant writes down the drafted role and changes its name to the drafted name |
| Keep as Assistant | The role and name stay as they are |

The final proposal takes the form of an [Autorun](https://agi-labo.com/en/tools/cockpit/docs/autorun) addressed to itself if you are an AGI Lab member, or an item added to what it looks at during periodic checks if you are not. Either way, it acts only after you agree.

If you close the window without talking to the Assistant, it remains with its set name and role. Select it in the task list to continue the conversation at any time.

## Talk without connecting a chat

You can talk with a Crew in its conversation inside Cockpit. You do not need to connect Discord, Slack, or Chatwork (Chatwork is for members). You write requests the same way as for a task: type in the input field and send.

When you also want to talk with it from a chat, go on to [Connect a chat](https://agi-labo.com/en/tools/cockpit/docs/crew-chat).

## Create one from the New Task screen

1. Select **Create new** (+) at the top left of the window, above the task list.
2. At the top of the screen, choose **Crew** from **Task** and **Crew**. The first time you choose it, the **What is a Crew?** explanation opens; read it and press **Get started**.
3. Press the place name in the heading "What kind of Crew should work in ...?" and choose where the Crew works. If you do not choose a project, it works in **Its own folder (no project)**.
4. Enter a name in **Crew name (optional)**. Up to 32 characters. If you leave it empty, Cockpit creates the Crew with a temporary name and the Crew proposes a name.
5. Choose the agent and the approval mode. For how to choose an approval mode, see [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval).
6. In the input field **What you want this Crew to take on (optional)**, write what you want to hand over and send it. You can also create the Crew with it empty.

Once created, the Crew's conversation opens. If you did not write what you want to hand over, the Crew's settings open too.

| What you want to hand over | The Crew's first reply |
| --- | --- |
| Written | It drafts a role from what you wrote and shows it. If something is missing, it asks first |
| Not written | It asks what you want to hand over |

Some points trip people up.

- **Terminal and Antigravity cannot create a Crew.** Terminal does not appear in the list. If you choose Antigravity, "Crews can't run on Antigravity. Choose another agent." appears
- **With a file attached, you cannot create a Crew.** Remove the attachment, create the Crew, and pass files in the conversation afterward
- **When you reach the limit on how many you can create, you cannot create one.** The screen shows the limit and how to become a member. Delete an existing Crew to create one again

Pressing the **New Crew** button next to **Create new** opens the screen with **Crew** already chosen.

## Describe its role

You decide a Crew's role by talking with it.

1. Tell it what you want to hand over. The Crew shows a draft role. The draft covers how it replies, what it may do on its own, and which operations need confirmation.
2. Read the draft and say what you want to change.
3. When you agree, the Crew writes that down as its role.

**You can change the role by talking as many times as you like later.** Ask something like "I want you to take this job too, so add it to your role." For where the role is written, see [Home and memory](https://agi-labo.com/en/tools/cockpit/docs/crew-home).

## Turn a task into a Crew

You can turn an ongoing task into a Crew as it is. The conversation so far remains, and the Crew continues from there.

1. In the task list, open the menu of the task you want to turn into a Crew.
2. Choose **Turn into a Crew**.
3. When the confirmation appears, read it and go ahead.

The confirmation says the following.

- The conversation so far remains, and it continues from there as a Crew
- In the task list, this task appears as a Crew
- Child tasks and Autoruns addressed to this task also move to the Crew
- If it has a parent task, it leaves that parent
- **It cannot be turned back into the original task**

Once it becomes a Crew, the Crew drafts a role from the conversation so far and shows it. When you agree, it writes that down.

You can turn into a Crew a Native UI task of an agent that can run a Crew, or a Cockpit task. While the task is working or waiting for an approval or an answer, "This task is working or waiting for an approval or an answer. Turn it into a Crew after its turn ends" appears. Try again after the turn ends.

## If you stop partway

| Where you stopped | What happens next |
| --- | --- |
| Closed during first-run setup | It does not resume where you left off. If setup appears the next time you open Cockpit, it starts from the first screen. If it does not appear, open it again as in the next row |
| Finished setup without creating the Assistant | Open **Setup** from the app menu in the lower-left corner and press **View again** for **First-run setup** to go through it from the start. If you have no Crew yet, it creates the Assistant at the end |
| Closed during the Assistant's first job | The Assistant and the conversation remain. Select the Assistant in the task list to continue |
| On the New Task screen, the Crew was created but the first send failed | Send again, and it is resent to the same Crew. You do not end up with 2 Crews |

## Related pages

- [What is a Crew](https://agi-labo.com/en/tools/cockpit/docs/crew-about)
- [Approval modes and permissions](https://agi-labo.com/en/tools/cockpit/docs/crew-approval)
- [Connect a chat](https://agi-labo.com/en/tools/cockpit/docs/crew-chat)
- [Home and memory](https://agi-labo.com/en/tools/cockpit/docs/crew-home)
- [Initial setup](https://agi-labo.com/en/tools/cockpit/docs/initial-setup)
