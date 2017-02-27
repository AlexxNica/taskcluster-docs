---
title: Hooks
order: 50
---

The [Hooks service](/reference/core/hooks/) is responsible for creating
pre-defined tasks in response to external events.

At the moment, it only supports creating tasks at particular times, but it will
soon be expanded to support triggering tasks from arbitrary web hooks and/or
pulse messages.

## Using Hooks

Hooks are named with a `hookGroupId` and a `hookId`. The group IDs follow a
pattern given in the [namespaces document](/manual/devel/namespaces). The
`hookId` is arbitrary, although it is a good idea to think carefully about the
names and use long, hierarchical names.

The scopes available to a hook are given by a role. This allows separation of
hook management from scope management, and the full generality of scope
expansion. The role for a hook is named `hook-id:<hookGroupId>/<hookId>`. The
role must include all of the scopes required to create the task, including
`queue:create-task:<provisionerId>/<workerType>`.

The scopes actually used by the hook's task are, of course, defined in
`task.scopes`, which must be satisfied by the hook's role. These need not
include `queue:create-task:<provisionerId>/<workerType>` unless the task will
be creating more tasks (for example, a [decision task](/manual/tasks/decision)).

## Advice

Hooks are not easy to manage directly, and exist far from the rest of the
infrastructure for your project. Try to avoid embedding too much detail into
the hook definition.

For simple work (for example, a periodic cache refresh), create a shell script
in your code repository, and write a hook that will check out the latest source
and run that script. Then any modifications to the cache-refresh process can be
handled using your usual development processes, instead of an update in the
hooks API.

For more complex purposes, invoke a [decision task](/manual/tasks/decision)
thad runs within a source checkout and draws the details of what to achieve out
of that source checkout.