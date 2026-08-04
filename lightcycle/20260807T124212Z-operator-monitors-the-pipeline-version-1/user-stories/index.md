# User Stories

## Get oriented on the pipeline

**Goal:** See the whole pipeline at a glance - what's active, blocked, waiting on me, and queued - the moment it changes, as long as the dashboard is running.

**Actor:** Operator

### Launch the dashboard

- [See the dashboard connect and render](61c7bcfe-647f-412b-9ea2-cdde9a0dec6a.md)
- [See whether the pool is running](34603aed-522d-4835-9836-00b99abf31ea.md)
- [See which engine version is running](131f76a9-7b9b-4d70-889c-5c0e9c9a0d3d.md)
- [See when a newer version is available](2a051776-fce5-47ac-bef5-ceddf14045d7.md)
- [Quit the dashboard](204b730c-94db-43f2-b0e0-4c685e37a038.md)
- [See a contextual shortcut bar in the footer](75374a1f-589b-421e-b5be-2191407b5d9a.md)
- [See whether Claude is available to process work](02c20dfb-88af-4b91-887b-6c9f1fa660aa.md)

### Review current work status

- [See needs-attention items pinned to the top](7da675a5-6a5a-4ff9-a824-a92b519e17fe.md)
- [See active items with elapsed time](5bf2d324-a842-4390-aa68-32928145a70b.md)
- [See queued items at the bottom](019d51d5-4952-4e0e-bb5d-748173e5e069.md)
- [Scroll the list with arrow keys](3c7be8c5-633b-4049-b925-1311fb82abc9.md)
- [Hear an alert when something needs me](5d060c74-c1a8-40d5-934a-0b1a4f382cb3.md)
- [See a quiet message when there's no work at all](bdbc1948-c482-400b-843d-9ba46b792ed1.md)
- [Keep my selection on an item as it moves](f4c18d86-2dbd-40ae-9d41-5fc98cea4347.md)
- [See a dependency indicator on blocked items](fecce582-e41a-4490-9158-48c2eab2c7db.md)
- [See which project an item belongs to](1ccf2602-259e-4889-844e-fe766119f43b.md)

## Inspect one item's journey

**Goal:** Understand a specific item end-to-end - its artifacts, child steps, and logs - to see what happened or why it's stuck.

**Actor:** Operator

### Find the item

- [Open the item detail view](18fdcc37-373a-4b2c-a045-18a1c20e0fc3.md)
- [See the item's current step, role, and elapsed time](45d7d629-f736-4875-8470-f928558a88ea.md)
- [Return to the list without losing position](265befe7-7a49-4219-8f4c-8aabee2f201d.md)
- [See why an item is escalated](4bee4625-16c6-4f37-a7af-01893eba78ea.md)
- [Land on the tab that matches the item's status](74dd1219-8a28-4c16-a293-83a82b4db930.md)
- [Cycle tabs with [ and ]](3480e340-d1f3-44ac-a0d8-e6ef269f6d13.md)

### Explore the hierarchy

- [See which nodes have content](9c53daa8-4b1a-425e-a3be-4f4a2ba53849.md)
- [See the hierarchy from its root node down](f448a52c-0755-4ded-bea1-7992a79555e4.md)
- [See each node's id, status, and role in the hierarchy](833b6039-ab42-4b6a-b1d7-e757b9dc9233.md)
- [Scroll the hierarchy with the keyboard](92e81697-0e0b-4290-ac26-ba46ca3f680d.md)
- [Open a node from the hierarchy](ccd1d6ac-5812-43f6-a9bc-4cdfb667fa57.md)
- [Return to the hierarchy tab without losing position](ded8dbd1-c1ef-45c0-97df-562b72a52e91.md)
- [Keep ancestry visible while I scroll](eaa87e0b-0a4f-4c35-b3ff-d372ec1a286f.md)
- [Jump straight to a node's artifacts or log from the hierarchy](8f6e008e-f9d8-4c6d-a061-02da7002bd42.md)
- [See the current node highlighted in the hierarchy](3b552243-e146-426c-b0e1-2c3c0d2b3801.md)

### Explore the artifacts on a node

- [See the artifacts attached to a node](da91328b-2935-4b00-8444-63bd41cf0263.md)
- [Select an artifact to open it](0bbd3f0a-5167-4316-90be-e9cca4fd0d3e.md)
- [Return to the node without losing position](40b0de53-450b-4312-bb08-4d57c11a08ed.md)

### View an artifact

- [View a text artifact full-screen](1135fcfe-14a6-41d6-934c-b91e58c5072d.md)
- [Follow a URL artifact](37bbed50-e813-4968-b9cf-3d16383afda9.md)
- [Open a file-path artifact in its application](c70a4260-66c0-4e47-836e-b08e2792fefb.md)
- [View a list artifact as a scrollable list](e01a64c6-91e2-40b0-bae2-0ce1f6b50430.md)
- [Return to the artifact list without losing position](9f5a722e-3850-4886-802f-4a38914784a7.md)

### Stream the current step's live log

- [See the live log tail as it's written](28a501f3-8018-418b-b0f0-f068a928eb2c.md)
- [See that a step has no live log to stream](8ff7f352-1a1b-4f3f-b557-2a5cd164a8ff.md)
- [Scroll back through the live buffer](36a4fe94-2feb-4b79-a740-6a18fa6893c1.md)
- [See when the step finishes while I'm watching](a0a38358-9b11-4966-abd8-17edf7fd5987.md)
- [Return to the node without losing position](2f58b727-2ff9-4f23-b2d5-96e4c1b63d19.md)
- [See a done step's historical log](3c609f13-cc69-4772-92a3-e2966d4a234e.md)

## See what's coming next

**Goal:** Know what's waiting in the backlog so nothing surprises me.

**Actor:** Operator

### See the todo backlog

- [See todo items not yet activated](a0c35689-d78b-4ee0-9cde-bab92a9648af.md)
- [Explore a backlog item's context in the hierarchy](7f92f015-2d19-449f-bb47-f389e6dc9691.md)
- [Return to the backlog without losing position](572ae397-7fb6-453c-832a-80700e1bd483.md)
- [Switch to the backlog with a tab or shortcut](5fa1445f-dc04-4322-b1be-e00496e8d0d9.md)
- [Filter the backlog by project](b4b5ce79-8c4b-4fb4-a4c9-1a040e4c6bb7.md)
- [See a quiet message when the backlog is empty](addfdbfd-a67a-43f5-a343-dd2f45af15ff.md)

---

← Back to [Operator monitors the pipeline - Version 1](../index.md)
