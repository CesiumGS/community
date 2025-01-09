# Code Review Guide

This guide aims to establish best practices for conducting code reviews across all Cesium projects, promoting a culture of craftsmanship, shared ownership, and continuous learning. It's language agnostic, providing a framework that can be customized with specific tooling or style guides for each project. By providing clear guidelines, this guide aims to enhance code quality and consistency, streamline collaboration, facilitate knowledge sharing, and foster shared ownership.

## Table of Contents

- [Intro: Why We Review Code](#intro-why-we-review-code)
- [Tone: Seek Great Code and Motivated People](#tone-seek-great-code-and-motivated-people)
- [Branch Management and Commits](#branch-management-and-commits)
- [Managing Updates to the Public API](#managing-updates-to-the-public-api)
- [When to Open a PR: Scope, Timing, and Expected Level of Effort](#when-to-open-a-pr-scope-timing-and-expected-level-of-effort)
- [PR Descriptions: Creating a Historical Record](#pr-descriptions-creating-a-historical-record)
- [Communicating Status with Automation, CI, and GitHub Tooling](#communicating-status-with-automation-ci-and-github-tooling)
- [CLAs and Considerations for Open Source](#clas-and-considerations-for-open-source)
- [Reviewers and Code Owners](#reviewers-and-code-owners)
- [Hitting the Merge Button: Responsibility and Accountability](#hitting-the-merge-button-responsibility-and-accountability)

## Intro: Why We Review Code

> Don't worry that you might not find anything to comment on, or that you don't know enough about every area of the code. There will usually be something to say about almost every commit; even where you don't find anything to question, you may find something to praise. The important thing is to make it clear to every committer that what they do is seen and understood, that attention is being paid.
>
> — [Producing Open Source Software](https://producingoss.com/en/producingoss.html#code-review)

Pull requests are integral to Cesium's software development process, but are also one of its most challenging and time-consuming aspects. We review code to:

- **Improve code quality and consistency:**  Identify and correct bugs, optimize performance, and ensure adherence to coding standards, promoting a robust and uniform codebase.
- **Collaborate:** Encourage open and candid discussions about code implementations, which helps align the team on best practices and design decisions.
- **Share knowledge:** Transfer institutional knowledge among team members, such as best practices, available shared systems or infrastructure.
- **Foster shared ownership:** Establish a sense of collective responsibility for the project, where quality and success are seen as a team effort.

## Tone: Seek Great Code and Motivated People

We often say, "comments are about the code, not the people." This is a good starting point. We can point out deficiencies in the code without implying anything about the code's author.

Ideally, the person writing the code should receive feedback in the same spirit, and take the review comments as positive opportunities to discuss and improve. However, it's natural to be a little discouraged to hear that your code is still not good enough.

A little empathy on the reviewer's part can go a long way toward keeping the contributor motivated. A few tips:

- Always acknowledge the contributor's effort, and thank them for it.
- Start with context. If the PR is mostly on track and only needs a few tweaks, make sure to say that.
- If larger changes or a refactor is needed, try to frame the feedback as the start of a discussion, rather than a simple critique.
- Phrase comments as questions, where appropriate. Questions invite discussion, and can also be a more gentle way to suggest that the code is not as clear as it could be.

## Branch Management and Commits

Git branches enable collaboration and help us avoid forcing work-in-progress upon others before it's ready. A few tips:

- Keep each branch / pull request as focused as possible. Multiple unrelated changes grouped together make reviewing more difficult.
- In particular, avoid mixing large-scale, cross-cutting changes such as search/replace, code reformatting, or code regeneration together with other changes. The other changes - which require more scrutiny during review - risk being lost in the noise of the large-scale changes.
- Do not force push or rebase. When merging a pull request, be sure to choose "Create a merge commit" instead of "Squash and merge" or "Rebase and merge". Doing otherwise rewrites Git history, which impedes collaboration by creating conflicts for others that may have built upon your work. Remember that we can't always tell if someone has built upon our work!
- As the one exception to the above, it is ok to rebase changes made locally that have not yet been pushed up to a public repo.
- Enable [automatic deletion of branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-the-automatic-deletion-of-branches) after pull requests are merged in your repository. This helps keep the list of branches tidy, and GitHub allows a branch to be restored from a PR should that ever become necessary in the future.

With complex sets of changes, consider creating a sequence of pull requests, each of which builds upon the previous. For example, you might create a branch off `main` called `refactor` to do some refactoring, and then create another branch off the `refactor` branch to add a new feature that requires the refactoring. Then, open two pull requests:

- The first is the pull request of the `refactor` branch into `main`.
- The second is the pull request of the feature branch into `refactor`.

This arrangement makes it easy to review each step separately. Once `refactor` is merged into `main`, GitHub will automatically change the feature branch pull request to target `main` instead. If the two PRs were both directly into `main` instead, then the diff shown with the second one would also include all the changes from the first, making it much harder to review. 

## When to Open a PR: Scope, Timing, and Expected Level of Effort

A PR should be opened as early as the work is considered to be in a stable state. Some things to keep in mind are:

- Scope: A PR should be small, targeted, and atomic. We'd prefer many small PRs than few large PRs, as the later creates a large burden for the reviewer.
- Timing: Review and respond promptly, ideally within 24-hours. This maintains momentums and investment. If you can't review within that time, leave a comment

### Draft PRs

Utilize a draft PR to allow others to look at code before all portions are complete. This may be particularly useful for larger changes or changes that will affect multiple github repositories.  This will allow others to review your approach to the problem


## Managing Updates to the Public API

Changelogs and release notes should provide clear and open communication about project changes and updates, manage user expectations, and promote new features. The changelog serves as a detailed record, tracking changes on a per-PR basis and simplifying the creation of release notes.

- **Document changes in the changelog:** Ensure that every change to a public API– whether it's an addition, fix, deprecation, or breaking change– is recorded in `CHANGES.md`.
- **Update documentation:** Alongside code changes, update the reference documentation to reflect new features or modifications in the API. Include examples and guidance to help users understand how to use the new or updated features effectively.
- **Have a versioning strategy:** If the changes are significant and potentially break backward compatibility, verify that the PR is prepared for a version bump in accordance with the project's versioning strategy (e.g., according to Semantic Versioning principles).
- **Highlight deprecations and breaking changes:** Clearly mark any deprecations or breaking changes in the PR description. Provide or discuss a clear migration path and, if applicable, a deprecation timeline.

## PR Descriptions: Creating a Historical Record

Pull requests also serve as a historical record of a change. This is important because there is context that everyone on the team knows now, but may not obvious later. Documenting this context in the pull request will help preserve it future reference.

- **Summary of the change:** A short summary of the major change(s) can be helpful for understanding the pull request quickly.  This can be helpful for reviewers, as well as future readers to get a high level overview before trying to read the code.
- **Why is this change being made:** Typically pull requests are opened to fix a bug or add a new feature.  If so, the pull request should include a [link to the issue](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue). The PR may also clarify why the implementation was done in a certain way.
- **Test plan:** A set of steps that exercise the new change.  This makes it clear how to run the code even if it not obvious in the UI.  Make sure to include details about any special configuration or data needed to test the code that may not be typically used.

Consider adding a [pull request template](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository) that reminds authors to include this information.

### Linking

Make use of links when authoring a pull request. They allow the reader to get additional context without digging. If possible, try to use an anchor link that jumps directly to the relevant content. Below are some types of links that are good to include in a pull request if relevant:

- Issues (either for the current product or other repositories)
- Community forums or support threads
- Standards or API documentation (such as [MDN](https://developer.mozilla.org/en-US/))
- Change logs for dependencies
- Best practices documentation such as blogs or guides

### How to find a PR from a code change

1. Find the commit hash
1. [Search for PRs that contain the commit hash](https://docs.github.com/en/search-github/searching-on-github/searching-issues-and-pull-requests#search-by-commit-sha)

## Communicating Status with Automation, CI, and GitHub Tooling

Github workflows should be set up for your repository in order to provide basic status of your branches.  The automation should enforce your style guide through linting, code quality though unit tests and code scanning, as well as security through secrets checking, and vulnerability scanning.

Review each separate check/stage in the pipeline:

1. Ensure each stage passes
1. Ensure there are separate stages for: unit testing, linting, and security scanning
1. Check the artifacts for each stage to ensure 'silent failures' are not present

## CLAs and Considerations for Open Source

To ensure the health of a project with external contributors, there are a few more considerations:

- **Contributor instructions:** [`CONTRIBUTING.md`](https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/setting-guidelines-for-repository-contributors) should document clear guidelines for contributing to your repo. Consider adding a link in your [pull request template](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository) to remind authors of required processes.
- **Contributor license agreement (CLA):** All contributions require a signed [CLA](https://github.com/CesiumGS/cesium/blob/main/CONTRIBUTING.md#contributor-license-agreement-cla) to protect the project and contributors. One must be submitted before a Cesium team member can review. Set up [automated checks via GitHub actions](https://github.com/CesiumGS/cesium/tree/main/.github/actions/check-for-CLA) to verify CLA status when pull requests are  opened.
- **Third-Party licenses:** Keep an updated list of all third-party libraries and their licenses in `LICENSE.md`. Ensure your project’s license is compatible with the licenses of any new third-party libraries used (e.g., MIT, BSD, or Apache 2.0).

### Rejecting Pull Requests

Rejecting a pull request can be challenging, but it's sometimes necessary to maintain the project's scope. Here are some guidelines to handle rejections respectfully and constructively:

1. **Always begin with thanks.** For example: "Thanks for your contribution and for engaging with our project!"
2. **Acknowledge their needs as valid,** even if the contribution doesn’t align with the project’s goals.
3. **Be clear and constructive** about why the PR does not fit with the project.
4. **Present ways out,** such as a workaround or forking the repo.
5. **Encourage future contributions.** For example: "Please consider contributing again; we're looking forward to seeing more of your work!"

## Reviewers and Code Owners

 - **Velocity**: Reviewers should be concise and direct in their feedback. The goal in reviews should be to facilitate continuous improvement of the codebase with high velocity rather than achieving perfection in each PR.

 - **Leveling up the team**: Reviewers should aim for their feedback to be of high value and to be sought after and valued by other engineers, as reviews are an important tool in leveling up teams.

 - **Code Owners**: Teams should consider adopting formal code owners policies, which can be facilitated using Github code owners. This feature is particularly recommended for critical path code. A team’s practice can be flexible, for example the assigned code owner could be allowed to reassign reviews to teammates, the important part is that code owners should ensure that certain developers see and have the opportunity to review all PRs touching critical areas of code. 

### What to review:
 - **Design**: Review that the overall approach makes sense and code in the PR interacts well with other parts of the system. Verify that the code belongs in the project, and that now is the right time to add it.

 - **Execution**: Check if the new code does what was intended. Consider if what was intended is good for both software end-users and developers who will use the code in the future. Consider edge cases and concurrency problems.

 - **Performance**: Is this a feature where performance matters? If yes, check that the author made sound decisions with respect to compute and memory usage tradeoffs when selecting data structures and algorithms and when writing code. Check that the PR includes performance statistics that maintain previous baselines and meet feature requirements and project performance best practices (eg: [Performance Profiling with Unreal Insights](https://github.com/CesiumGS/cesium-unreal/blob/main/Documentation/performance-profiling-with-unreal-insights.md), [CesiumJS Performance Testing Guide](https://github.com/CesiumGS/cesium/blob/main/Documentation/Contributors/PerformanceTestingGuide/README.md), and [WebGL Debugging and Profiling Tools](https://www.realtimerendering.com/blog/webgl-debugging-and-profiling-tools/))

 - **Complexity**: Consider if the code is more complex than needed. Check if it is difficult to read now or appears difficult to maintain by developers in the future. Review if code is over-engineered in terms of being too generic or implementing functionality that is not presently needed in the system.

 - **Third party libraries**: Check that the author made sound build vs. buy decisions with respect to using third-party-libraries.

 - **Style**: Check that the PR conforms to the style conventions in the project. Check that the author picked good names for items that convey what they are for and are easy to read. The style of the codebase should be consistent, as if written by one person, and new features should look as if they have always been there. Reviewers should link directly to Cesium’s coding style guides (eg. [CesiumJS Coding Guide](https://github.com/CesiumGS/cesium/blob/main/Documentation/Contributors/CodingGuide/README.md), [C++ Style Guide](https://github.com/CesiumGS/cesium-native/blob/main/doc/style-guide.md))

 - **Tests**: Check that the PR contains unit tests, integration tests and end to end tests. The tests should build confidence in the software by verifying the meaningful functionality of the code. Tests should be composed of simple and useful assertions which fail when they should without false positives & negatives. Reference Cesium guides for tests when needed (eg: [CesiumJS Testing Guide](https://github.com/CesiumGS/cesium/blob/main/Documentation/Contributors/TestingGuide/README.md), [Testing Improvements in CesiumJS](https://cesium.com/blog/2022/05/12/testing-improvements-in-cesiumjs/), and [Open Source Development Practices](https://cesium.com/blog/2012/11/14/open-source-development-practices/#tests))

 - **Documentation**: Check that the PR contains useful comments explaining why code exists instead of what it does. Ensure PR changes are documented in all areas including code, getting started guide, architecture diagrams, supplemental docs for external tools (eg. VS Code, Unreal Insights)

  - **Security**: TODO

## Hitting the Merge Button: Responsibility and Accountability

Reviewers play a critical part in ensuring that code at Cesium remains high quality. It is the responsibility of all of us, as reviewers, to ensure that the code we review meets the quality expectations of the rest of the team.

Accepting and merging a pull request means that the reviewer is accepting some responsibility for the code being merged. The author of the code is still largely accountable for any critical issues that immediately arise, but the reviewer shares some accountability as well. It's important then to ensure that a quality code review is performed.

Quality code reviews can be broken down into 5 levels of thoroughness:

1. **Rubber stamp.** Suitable for pull requests which only deal with documentation updates. The reviewer looks at the changes but doesn't go deeply into them. _Code changes, even minor ones, should **never** be rubber stamped._
2. **Skim the code and ensure all CI automation is passing.** Suitable for small pull requests. The reviewer skims through the code changes and makes sure that all CI automation passes, included automated unit and integration tests.
3. **Compile locally and run the code.** Suitable for most pull requests. The reviewer compiles and runs the code locally, using the testing instructions and test data included in the PR. This should be done for major feature pull requests and any larger fixes or refactors.
4. **Deep read of the code changes.** Suitable for most large pull requests. The reviewer goes through line by line to ensure there are no logical or major style issues. Where the reviewing developer is intimately familiar with the code in question, and can reasonably predict the logical outcomes. The majority of pull requests and reviews will likely be this layer.
5. **Synchronously review one-to-one with the author.** Suitable for giant pull requests. The author meets with the reviewer and goes through the code changes one by one. This, like pull requests with more than 1500 changes, should be exceptionally rare.

These are guidelines, and exceptions can and will happen. Exceptions should be in the direction of being more thorough, not less. Some small pull requests may need you to compile and run locally, and as reviewers we should learn to identify these moments. If you have doubts, do a deeper review. If you still have doubts, ask for help.

Remember that reviews should also be public. If you do meet in a video call or in a room, the reviewer should summarize the discussion in a PR comment. This helps everyone looking at the PR later and ensures we are transparent about the life of the PR.

An approving reviewer is also responsible for merging the PR except in repositories where the Merge Queue is in use. There may be exceptions here, such as if the author or another reviewer requests multiple reviews they should clearly indicate such in the body or comments of the pull request. In the case of multiple reviews, the author and reviewers should discuss and decide who the responsible party for merging will be.

If the repository is not configured to automatically delete branches on merge, the person merging the branch should ensure the branch is deleted post-merge.

Authors, or a suitable Cesium representative in the case of an open-source issue, are responsible for ensuring that related issues are closed. Where possible, [linking keywords should be used either in the PR description or a commit messge] to ensure that issues are closed automatically.
