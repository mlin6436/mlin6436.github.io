---
layout: post
title: "Gradle Build Lifecycle"
tags: ["gradle", "lifecycle"]
---

<div class="message">
It's easier to find out what Gradle does on the surface, but the knowledge can only stretch to a level that understanding what Gradle does underneath the hood, the build lifecycle is important.
</div>

### Vocabulary

- Projects are registered in `settings.gradle` file
- Most of the time, a project has a `build.gradle` file
- A `Task` is a representation of actions (default or custom) that specifies the steps required to produce an artifact

### Build Phases

- Initialisation
    - Use `settings.gradle` to identify __all the projects__ (single/multi project) involved in the build process
    - Create an instance of `org.gradle.api.Project` for each project
- Configuration
    Evaluate `build script` (a.k.a `build.gradle`) of each project, and __constructures a DAG of tasks__
    {% include image.html url="/media/dag.png" width="100%" description="DAG" %}
    __NOTE__: [Configuration on demand](https://docs.gradle.org/current/userguide/multi_project_builds.html#sec:configuration_on_demand), this gives the project the ability to configure only the relevant projects during the build process
    __NOTE__: find out task dependency using `--dry-run` or [build scan](https://scans.gradle.com/)
- Execution
    Identify required tasks from the previous step and execute them according to their __dependency order__

In short, the build phases can be summerised as the following diagram

{% include image.html url="/media/lifecycle.png" width="100%" description="Lifecycle" %}