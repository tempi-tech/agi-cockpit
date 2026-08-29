<!-- Generated from tempi-tech/AGICockpit — do not edit directly. -->

# Creative Studio

Create 3D models and picture books while managing workspaces, artifacts, versions, review checks, and exported deliverables.

> Verified with AGI Cockpit 4.63.0 on 2026-08-29. [View the official documentation](https://agi-labo.com/en/tools/cockpit/docs/creative-studio)

Creative Studio gives 3D-model and picture-book tasks a managed production workspace and a dedicated artifact review surface. The task conversation, production files, previews, validation results, and version history remain available from one task.

## Choose what to create

Select **Creative Studio** on the new-task screen, then choose one of these production types.

| Type | Typical deliverables | Review surface |
| --- | --- | --- |
| 3D model | GLB, STL, STEP, and other model files appropriate to the request and tool | 3D preview, review sheet, Spec, Checks, Files, History, and Log |
| Picture book | Cover and page images, manuscript, PDF, fixed-layout EPUB, and related files | Page reader, manuscript, page checks, Files, History, and Log |

Select an existing workspace of the same type to continue its artifacts and history. Select **New workspace** to begin a separate production. Selecting a workspace does not start work by itself; the task begins when you submit the description of what to create.

## Create a 3D model

Describe dimensions, purpose, material or manufacturing method, holes or moving parts, and required file formats. For dimension-critical parts, include units and acceptance conditions. Explain how the result will be used, such as mounting with M4 screws or moving after printing, so the agent can derive meaningful checks.

If the first task shows a modeling-toolchain decision, choose Cockpit's managed setup or an existing system environment. Managed-tool availability depends on the operating system and processor. The setup screen reports the current support and download requirements. When the required toolchain is incomplete, the task should report what is missing instead of registering an empty artifact.

Once a model is registered, the right panel shows its 3D preview. Use view presets, auto-rotation, and maximize to inspect the shape. Review the sheet, dimensions, file formats, watertight result, and any motion or collision checks required by the request. A visible preview alone does not prove dimensional accuracy or manufacturability.

## Create a picture book

Along with the story brief, you can choose target age, page count, art style, font, text handling, image provider, and autonomy. For publication work, choose Kindle eBook, Paperback, or Hardcover before generating final art. Paperback work also depends on trim size, bleed, paper and ink, cover finish, and author-name treatment.

Autonomy can finish without confirmation, ask once after the initial direction, or ask after every page. When confirmation is enabled, the agent attaches images to an Ask and waits for a decision. That Ask confirms creative direction; it does not authorize publishing, purchasing, or another external action.

The artifact panel provides a reader for the cover and every page, plus page text, page-level checks, manuscript, files, history, and logs. Export copies generated EPUB, PDF, and related files to a selected destination. Exporting does not upload or publish the book to KDP or another external service.

## Review artifacts and versions

A workspace can contain multiple artifacts, and every artifact can have multiple versions. Create a new version for a revision instead of overwriting files and losing the review trail. The parent version and change summary show which result was used as the starting point and what changed.

Checks can report `pass`, `warning`, or `fail`. Confirm that the expected checks exist, that their evidence agrees with the files and logs, and that the final pages or model were actually inspected. A Log is diagnostic evidence and may display only its latest portion.

## Start and display Studio work from the CLI

Pass the production type through `--studio`. Cockpit creates a managed workspace, so normal `--directory` and `--worktree` selections do not apply.

```bash
cockpit task create --instruction "Create a mounting plate with four M4 holes" --studio model
cockpit task create --instruction "Create a picture book for a four-year-old about a lost fox cub" --studio picturebook
cockpit side-panel artifact latest
```

When a picture-book task's managed instructions call for `cockpit picturebook`, use it from that task's Studio workspace. You do not need to assemble the packaging pipeline manually from a normal task. See the [`cockpit task` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/task) for creation options and the [`cockpit side-panel` reference](https://agi-labo.com/en/tools/cockpit/docs/cockpit-cli/reference/side-panel) for artifact display.

## Finish safely

Before completing the task, confirm the export destination, final version, checks, and required file formats. Completing the task does not delete exported files or the managed workspace's artifacts. Creative Studio does not automatically publish, order, print, sell, or license the result.

See [Results and tools](https://agi-labo.com/en/tools/cockpit/docs/results-and-tools) for shared file and log review, [Ask](https://agi-labo.com/en/tools/cockpit/docs/ask) for human decisions, and [Security and data](https://agi-labo.com/en/tools/cockpit/docs/security-and-data) for storage and external-transfer boundaries.
