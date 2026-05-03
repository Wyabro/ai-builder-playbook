# A Practical Guide to Making Games and Apps with AI

AI can help you build games and apps faster than ever, but it does not magically turn a vague idea into a finished product. The builders who get the most out of AI do not simply keep asking for more code. They give the AI a clear target, work in small slices, verify the result in the real product, and cut scope before the project becomes impossible to finish.

This guide is written for people who want to actually build something. It covers games and apps, because the modern AI workflow overlaps heavily across both: planning, choosing tools, writing project rules, using coding agents, generating assets, testing, deploying, handling legal risk, and shipping updates. The guide starts with the beginner-friendly workflow, then gets more specific about engines, app builders, local models, multiplayer, AI safety, publishing, and prompt templates.

## The whole workflow in one page

If you only remember one thing, remember the order. AI-assisted building works best when each step gives the next step more context.

1. **Define the project**: Decide what you are making, who it is for, what platform it runs on, and what the first shippable version must include.
2. **Choose the simplest stack that can ship it**: Pick the engine, framework, or app builder based on the project, not on hype.
3. **Create the project contract**: Write a short instruction file that tells every AI session what the project is, what tools it uses, what counts as done, what to avoid, and how to verify changes.
4. **Set up the workflow**: Choose whether you are using a cloud coding agent, an editor agent, a local model, an app builder, or a mix.
5. **Build a skeleton**: Make the empty project run. Confirm the build path works before adding serious features.
6. **Build one slice**: Add one playable mechanic, one app flow, one screen, or one system at a time.
7. **Verify in reality**: Test the running game, deployed app, target device, build output, or store build. The AI saying “done” does not count.
8. **Cut scope continuously**: Keep a list of what is required now, what is deferred, what gets cut if blocked, and what belongs after launch.
9. **Prepare for release early**: Assets, legal provenance, AI disclosure, payments, performance, store review, and support are part of the project, not afterthoughts.
10. **Use AI after launch carefully**: AI can summarize feedback, draft patch notes, cluster bug reports, and create marketing drafts, but you should still decide the priorities.

The rest of the guide explains how to do each step without turning the project into a confusing pile of AI-generated code.

## Decide what you are building

Start by naming the kind of project. This matters because a good AI workflow for a tiny browser game is very different from a good workflow for a Steam game, a mobile app, or an AI-native product.

### The main project types

| If you are building | Start with | Why | Be careful about |
| --- | --- | --- | --- |
| A tiny playable web game | Plain JavaScript/TypeScript, Phaser, PixiJS, or Canvas | Fast to run, fast to share, easy for AI to understand, easy to deploy by link. Phaser has a Cursor starter template that connects Cursor rules with Phaser Editor MCP tooling ([Phaser GitHub](https://github.com/phaserjs/editor-starter-template-cursor-javascript)). | Browser games can accidentally turn into custom engines if you keep adding systems. |
| A polished 2D game | Godot or Phaser | Godot is approachable and good for 2D. Godot now has community MCP tools that let AI assistants create and edit scenes, validate scripts, inspect errors, and explore projects in the editor ([Godot Asset Library](https://godotengine.org/asset-library/asset/4767)). | You still need to understand scenes, nodes, signals, exports, and playtesting. |
| A lightweight 3D indie game | Godot or Unity | Godot is lighter. Unity has a huge C# ecosystem and official AI tooling, including Sentis for runtime model inference ([Unity](https://unity.com/features/ai)). | 3D adds camera, physics, animation, lighting, performance, and asset pipeline problems. |
| A cinematic or high-fidelity 3D prototype | Unreal Engine or Unity | Unreal is strong for high-end visuals, cinematic workflows, and UEFN. Epic documents release and royalty reporting workflows for Unreal Engine products ([Epic Developer Docs](https://dev.epicgames.com/docs/dev-portal/unreal-engine/release-forms-and-royalties/royalties)). | Do not overbuild gameplay if the goal is a visual or cinematic proof. |
| A platform-native creator project | UEFN, Roblox, or a similar creator platform | These platforms provide built-in audiences, publishing flows, moderation, and monetization systems. Epic says eligible Fortnite creators can receive engagement payouts based on program metrics ([Epic Games Developer](https://dev.epicgames.com/documentation/fortnite/engagement-payout-in-fortnite-creative)). | The platform controls rules, moderation, discovery, and payout mechanics. |
| A web app or SaaS prototype | Vercel v0, Cursor plus Next.js, Supabase/Firebase services, Bolt, Lovable, Replit Agent, or a normal web stack | AI app builders are useful for landing pages, internal tools, dashboards, CRUD apps, and quick SaaS validation. Vercel says v0 can import GitHub repos, create branches, open PRs, map previews to deployments, and work with production codebases ([Vercel](https://vercel.com/blog/introducing-the-new-v0)). | If there is no repo, export path, branch workflow, or test plan, the prototype can become trapped in the builder. |
| A mobile app | Expo React Native, Flutter, Swift, or Kotlin | Expo is often AI-friendly because TypeScript is widely understood and EAS Submit can upload production iOS and Android builds after store prerequisites are met ([Expo](https://docs.expo.dev/deploy/submit-to-app-stores/)). | Mobile adds signing, device QA, privacy labels, app review, payments, and screenshots. |
| An AI-native app or game | Usually web first, then native if needed | AI-native products need logging, cost controls, moderation, rate limits, prompt safety, and fallback behavior before polish. OWASP has separate guidance for LLM applications and agentic applications ([OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/?cat=44), [OWASP Agentic Applications Top 10](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)). | Live AI is a safety, cost, and compliance feature, not just a cool mechanic. |

### What the common tools actually are

Many guides name tools without explaining what role they play. Here is the practical map.

- **Cursor**: An AI code editor. Good for repo-based development, multi-file edits, asking questions about code, and using rules files. Cursor’s agent guidance emphasizes plans, rules, diffs, reviews, and scoped conversations ([Cursor](https://cursor.com/blog/agent-best-practices)).
- **Claude Code**: A coding agent that can work from the command line or GitHub Actions. It can read instructions from `CLAUDE.md`, respond to GitHub issues or PRs, use MCP tools, and create code changes for review ([Claude Code Docs](https://code.claude.com/docs/en/github-actions)).
- **GitHub Copilot coding agent**: A GitHub-integrated agent that can be assigned issues, make changes in branches, run tests and linters in GitHub Actions, and open PRs for human review ([GitHub Docs](https://docs.github.com/copilot/concepts/agents/coding-agent/about-coding-agent)).
- **Vercel v0**: An AI app builder that is strongest for web apps, UI, Next.js-style projects, prototypes, and production workflows connected to GitHub branches, previews, and PRs ([Vercel](https://vercel.com/blog/introducing-the-new-v0)).
- **Lovable and Bolt**: AI app builders that can quickly create web apps from prompts. They are most useful when you connect or export to Git before the project becomes important. Lovable documents GitHub sync for backups, branches, PRs, local development, and external deployment, while Bolt documents GitHub repository and branch workflows ([Lovable Docs](https://docs.lovable.dev/integrations/github), [Bolt Support](https://support.bolt.new/integrations/git)).
- **Replit Agent**: A prompt-to-app builder inside Replit. It is useful for fast iteration and publishing from a workspace, but serious projects still need the same review, testing, and export discipline as any generated app ([Replit Docs](https://docs.replit.com/core-concepts/agent)).
- **Ollama and LM Studio**: Tools for running AI models locally on your own machine. Ollama exposes a local API, while LM Studio provides a desktop app and local server with OpenAI-compatible endpoints ([Ollama](https://docs.ollama.com/api/introduction), [LM Studio Docs](https://lmstudio.ai/docs/developer/core/server)).
- **Continue and Cline**: AI coding assistants that can use local or cloud models. Continue supports models, rules, context providers, tools, and MCP servers in agent configurations, while Cline documents local model setups through Ollama or LM Studio ([Continue Docs](https://docs.continue.dev/reference), [Cline Docs](https://docs.cline.bot/running-models-locally/overview)).
- **MCP**: Model Context Protocol. In plain English, MCP is a way for an AI assistant to use tools. Instead of merely reading code and guessing, an MCP-connected assistant may inspect a game editor, query a database, drive a browser, read errors, manipulate scenes, or call a project-specific tool. MCP is powerful, but it also means the AI can affect real project state, so permissions, undo, and review matter.

One current warning: do not choose Firebase Studio as a default new-project tool. Google says Firebase Studio was announced for sunset on March 19, 2026, new workspace creation will be disabled on June 22, 2026, and the service will shut down on March 22, 2027, while core Firebase services like Firestore, Authentication, and App Hosting are not affected ([Firebase](https://firebase.google.com/docs/studio/migrating-project)). Firebase services can still be useful, but the Studio product should be treated as something to migrate away from rather than something to start with.

## Create the project contract

Before you ask an AI to build the project, write a small document that tells it what project it is inside. This is not busywork. It is how you stop each new chat from re-deciding your stack, broadening scope, or “fixing” unrelated files.

Different tools read different instruction files. Cursor uses project rules such as `.cursor/rules/`. Claude Code commonly uses `CLAUDE.md`. GitHub Copilot can use custom repository instructions. Some tools use `AGENTS.md`, `GEMINI.md`, or a plain project document. The exact filename matters less than the habit: the rules should live in the repo, and every AI session should read them first.

### Project contract template

```markdown
# Project Contract

## Project
- Name:
- One-sentence concept:
- Target player or user:
- Target platform:
- Target hardware or device:

## Stack
- Engine/framework:
- Language:
- Runtime:
- Backend:
- Database:
- Hosting/deployment:
- Asset pipeline:
- AI coding tools:
- MCP tools, if any:
- Runtime AI/model use, if any:

## The first shippable version
- Core loop or primary user flow:
- Required screens/scenes:
- Required systems:
- Required content:
- Accessibility baseline:
- Localization plan, if text-heavy:
- What counts as done:

## Cut list
- Deferred for now:
- Cut if blocked:
- Post-launch:

## AI rules
- Read this file before changing code.
- Work on one feature or bug at a time.
- Explain the plan before broad edits.
- Use targeted edits; do not rewrite full files unless asked.
- Do not change unrelated files.
- Ask before changing scope.
- Do not claim success without verification.
- If a new change breaks something, consider reverting before stacking fixes.

## Verification
- Local run command:
- Test command:
- Build command:
- Deployment command:
- Production URL or target device:
- Performance target:
- Manual smoke test:
- Production marker method, if web-deployed:

## Session handoff
- Changed:
- Broken:
- Next:
```

### Tell the AI what the end result should look or feel like

One of the most useful things you can do is describe the result in human terms before asking for code. Do not only say “add a menu.” Say what the menu should do, where it appears, how it feels, and how you will know it works.

Example:

```text
I want the title screen to feel like a small arcade game, not a corporate app.

End result:
- Full-screen title screen.
- Big readable game title centered near the top.
- One primary Start button.
- One smaller Settings button.
- Keyboard users can press Enter to start.
- Mouse users can click Start.
- The screen should work on a laptop browser and a phone browser.

Do not build the settings screen yet. Only add the title screen and transition into the existing game scene.
```

This kind of prompt gives the AI a destination. It also gives you a way to reject bad output without arguing about implementation details.

## Set up your AI workflow

There is no single correct AI setup. The right setup depends on your budget, machine, privacy needs, and tolerance for friction.

### Cloud-first workflow

This is the easiest path for most people. Use Cursor, Claude Code, GitHub Copilot, Cline with a hosted provider, or another cloud model. The model runs remotely, so your computer does not need to be powerful. The tradeoff is that you may pay for usage, you need internet access, and you need to be thoughtful about private data.

Use this if:

- You are new.
- Your computer is modest.
- You want the strongest models with the least setup.
- You are building a project that can safely use cloud inference.

Basic workflow:

1. Put the project in Git.
2. Add the project contract.
3. Use a coding agent in your editor or from the command line.
4. Ask for a plan before changes.
5. Review the diff.
6. Run the app or game.
7. Commit small working changes.

### Local-first workflow

Local models run on your own machine. The appeal is privacy, offline use, and no per-request API bill. The downside is that smaller local models can be slower, weaker at complex reasoning, and more fragile with tool use than the best hosted models.

Local tools are becoming practical. Ollama can run models and expose a local API, LM Studio can run models through a desktop app or local server, Continue can configure local models and tools for coding workflows, and Cline documents local model usage through Ollama or LM Studio ([Ollama](https://docs.ollama.com/api/introduction), [LM Studio Docs](https://lmstudio.ai/docs/developer/core/server), [Continue Docs](https://docs.continue.dev/customize/models), [Cline Docs](https://docs.cline.bot/running-models-locally/overview)).

#### Low-end machine

Use local AI as a helper, not as the main autonomous coder. Run smaller models for explanations, file summaries, simple code suggestions, naming, documentation, and “where should I look?” questions. Do not expect reliable multi-file agentic refactors.

Recommended pattern:

1. Use a cloud model for hard architecture or multi-file implementation if you can.
2. Use a small local model for private notes, brainstorming, documentation, and simple edits.
3. Keep context small. Feed it one file, one error, or one function at a time.
4. Avoid giving local agents broad write access unless you have tested them on throwaway code.

#### Mid-tier machine

Use local models for more serious coding assistance, but still keep expectations realistic. A mid-tier setup can often handle a coding-focused model well enough for chat, targeted edits, and smaller agent tasks. It may struggle with huge context windows, large repositories, or long autonomous sessions.

Recommended pattern:

1. Run a local model through Ollama or LM Studio.
2. Connect it to Continue, Cline, or another coding assistant that supports local providers.
3. Use it for read-only repo exploration, small bug fixes, tests, refactors, and documentation.
4. Use compact prompts and targeted context.
5. Use a stronger cloud model only for risky refactors, multiplayer architecture, security, or release reviews.

#### High-end machine

Use local models as a serious part of the workflow, especially for privacy-sensitive or cost-sensitive projects. With enough memory and compute, local coding models can handle broader context, longer sessions, and more capable agent workflows. Cline’s local model documentation notes that local models can be practical for real development work, while still not matching the best cloud APIs in speed or capability ([Cline Docs](https://docs.cline.bot/running-models-locally/overview)).

Recommended pattern:

1. Run a stronger local coding model through LM Studio, Ollama, or another local server.
2. Connect it to Continue or Cline.
3. Use local inference for repo exploration, routine implementation, tests, documentation, and private work.
4. Keep a cloud model available for second opinions when the risk is high.
5. Use Git branches so local-agent mistakes are easy to discard.

### Token budget and context

If you use cloud models, long context costs money and can slow work down. If you use local models, long context costs time and memory instead. Either way, context is not free.

A new chat also needs to be brought up to speed. If your tool has good codebase indexing, retrieval, or RAG, it can fetch relevant files instead of stuffing the whole project into the prompt. If it does not, the model may need many tokens just to understand the project again. That is why the project contract and session handoff matter.

Good habits:

- Keep generated files, build artifacts, dependency folders, and large exports out of AI context.
- Ask the agent to read only the files it needs.
- Restart when the conversation becomes confused, but paste the latest handoff.
- Keep each task small enough that the agent does not need the whole project in its head.
- Use local models for cheap exploration if cloud usage is a concern.

## Use the agent in phases

The most reliable workflow is chronological. Do not ask the AI to research, design, rewrite, test, and declare victory all in one breath.

### Phase one: research

Ask the AI to inspect the project without editing anything.

```text
Read the project contract and inspect the files related to [feature].
Do not edit yet.

Tell me:
- Which files matter.
- What the current flow is.
- What risks you see.
- What questions you need answered before changing code.
```

### Phase two: plan

Ask for a plan you can edit. Cursor’s Plan Mode is built around this idea: the agent researches, asks questions, creates a plan, and waits for approval before building ([Cursor](https://cursor.com/blog/agent-best-practices)).

```text
Create an implementation plan for [feature].

Include:
- Files you expect to change.
- The smallest useful version.
- Acceptance criteria.
- Tests or manual checks.
- Risks.
- What you will not change.

Do not edit files yet.
```

### Phase three: build

Approve one small step. This is where many projects go wrong: the user asks for one thing, and the AI opportunistically rewrites half the project. Prevent that.

```text
Implement step 1 only.
Use targeted edits.
Do not change unrelated files.
After the edit, summarize the diff and tell me how to verify it.
```

### Phase four: review

Read the diff. Ask the model to explain what changed. If the change is risky, ask a second model or fresh chat to review it.

```text
Review this change skeptically.

Look for:
- Unrelated edits.
- Broken assumptions.
- Performance problems.
- Missing tests.
- Security issues.
- Multiplayer or state bugs.
- Places where the implementation does not match the requested end result.

Do not modify files.
```

### Phase five: verify

Verification means checking the real product. It does not mean reading the AI’s summary.

For a game, verification might mean playing a full round, checking the frame rate, trying the build on a weaker device, or testing multiple browser tabs. For an app, it might mean submitting the form, refreshing the page, checking the database, testing auth, or opening the deployed URL. For a mobile app, it might mean running on a real device. For a Steam game, it may involve store build requirements and content review.

### Phase six: handoff

End every session with:

```text
Changed:
Broken:
Next:
```

This looks too simple until you need it. A future AI session can use those three lines to continue without rereading everything.

## Build the first version

### Start with a skeleton

The skeleton is the smallest version of the project that proves the build path. It is not the game yet. It is the empty stage where the game or app will live.

For a browser game, the skeleton might be a small TypeScript project with a `src` folder, an assets folder, a package file, and a blank game scene that runs in the browser. For a Godot game, it might be a project file, one scene, one script, export settings, and the project contract. For an app, it might be a Next.js or Expo project with one screen, one route, basic styling, and a working local run command.

Before adding features, make sure:

- You can run it locally.
- You can stop and restart it without weird manual steps.
- The project is in Git.
- The AI instruction file exists.
- The build command works.
- You know where the project will eventually deploy or export.
- If you are using MCP, the MCP tool works on a harmless test action before it touches real work.

This section should feel boring. Boring setup is good. It means future debugging will be about the product, not the foundation.

### Deploy at the right time

“Deploy early” does not mean every project needs a public launch page on day one. It means you should prove the riskiest delivery path before you depend on it.

For a static browser game or simple web app, deploy early because it is cheap and catches false success. A game that works locally but not on the live URL is not finished. For a backend-heavy app, deploy before you build the feature that depends on the backend. If the next feature needs auth, file uploads, payments, AI API calls, multiplayer relays, webhooks, or database writes, make sure the deployment path can support that before you build too far on local assumptions.

For mobile, “deploy” may mean creating a production build or internal test build. For Steam, it means understanding the Steamworks setup, store page review, build review, and content survey early. Steam’s Content Survey includes generative AI questions for games that used AI services during development or include AI services in the product ([Steamworks Documentation](https://partner.steamgames.com/doc/gettingstarted/contentsurvey)).

### Build one slice

The first slice should be playable or usable. It does not need to be impressive.

For a game, the first slice answers:

- Can the player enter the game?
- Can the player do the main thing?
- Does the game respond?
- Is there a score, win state, lose state, progress signal, or restart?
- Can another person try it without you explaining the code?

For an app, the first slice answers:

- Can the user reach the main screen?
- Can they perform the main action?
- Does data appear, save, or return?
- Is the obvious error case handled?
- Does it work in the target browser or device?

Prompt:

```text
Build the smallest playable/usable slice of this project.

End result:
[Describe what the user/player should see and do.]

Acceptance criteria:
- [Observable behavior]
- [Observable behavior]
- [Verification step]

Do not add extra systems, polish, settings, accounts, or monetization.
After the change, tell me exactly how to test it.
```

## Verify the work

The AI will often sound confident before the work is actually done. Verification is how you protect the project from that confidence.

### What counts as proof?

Here is the practical version of the source-of-truth ladder:

- **Best proof**: A real person uses the running product and the feature works.
- **Strong proof**: The deployed build, target device, store build, or real editor run shows the feature working.
- **Useful proof**: Automated tests pass, the build succeeds, and manual checks match the acceptance criteria.
- **Weak proof**: The AI says it changed the code, or the file looks right.

For example, if the task was “add a start screen,” the proof is not “the AI created `StartScreen.tsx`.” The proof is that the game opens to the start screen, the Start button works, keyboard input works if required, and the game transitions into the correct scene.

### Production marker rule

For web projects, use a production marker when you need to confirm that the deployed version actually contains your change.

Example:

```text
Change: Add the new start screen.
Marker: start-screen-v3-2026-04-30
```

After deployment:

1. Add the marker somewhere harmless, such as a data attribute, comment, or visible debug string that you later remove.
2. Push and deploy.
3. Fetch the deployed URL with no-cache headers.
4. Search the deployed response for the marker.
5. Only then test the feature on the live URL.

Local files do not count. Build folders do not count. A browser tab from ten minutes ago does not count. The point is to prove that the server or host is serving the version you think it is serving.

### Visible bug rule

If the bug is still visible, the bug is still real. Do not let the AI explain it away.

Use this prompt:

```text
The bug is still visible.
Treat the visible output as ground truth.
Do not explain it away.
Do not close the issue.

Start a fresh diagnostic:
- What are three possible causes?
- What is the smallest test for each cause?
- What should I see if that cause is true?
```

### Performance verification in plain English

Performance verification means checking whether the project still feels good on the machine, browser, or device it is meant for.

For games, this usually means:

- The game does not stutter during normal play.
- Input feels responsive.
- Visual effects do not tank the frame rate.
- The game still works after several minutes of play.
- A weaker target device can run it acceptably.

For apps, it usually means:

- Pages load quickly enough.
- Buttons and forms respond quickly.
- AI responses have a fallback if they are slow.
- The app does not become unusable after repeated actions.
- Mobile devices do not overheat or drain quickly.

If the AI adds particles, physics, realtime multiplayer, maps, image generation, voice, video, or live AI calls, ask for a performance check. The goal is not to become a professional optimization engineer. The goal is to catch obvious problems before they become deep architectural problems.

## Choosing engines and stacks in human terms

The stack choice should feel like a practical decision, not a list of logos. Each option below explains what the path is good at, how AI can help, and where a beginner can get trapped.

### Browser games

Browser games are the fastest path when you want someone else to play quickly. You send a link, they open it, and you learn something. That makes browser games excellent for experiments, jams, prototypes, small multiplayer rooms, arcade games, creative toys, and game-like apps.

Use Phaser or PixiJS when you want 2D sprites, scenes, UI, and arcade-style interactions. Use Three.js when the project truly needs 3D in the browser. If you use Three.js with a physics engine like Rapier, be careful: rendering and physics are two separate systems that must stay in sync. AI often changes the visible object without understanding the physics body, or changes the physics without understanding what the camera shows.

Good AI tasks:

- Create a title screen.
- Add a player controller.
- Add a score display.
- Add a restart flow.
- Add one enemy behavior.
- Add browser input support.
- Write a smoke-test checklist.

Risky AI tasks:

- “Make it multiplayer.”
- “Add procedural generation.”
- “Improve performance.”
- “Refactor the whole game loop.”

Those tasks are possible, but they need a plan first.

### Godot

Godot is a strong choice for solo 2D games and small 3D games because it is relatively approachable. It also has a project structure that can be easier for an AI to reason about than a massive custom engine.

The exciting part is MCP-style editor integration. If an AI can inspect scenes, attach scripts, validate GDScript, and read editor errors, it can help with more than plain text files. The Godot AI Assistant MCP plugin lists tools for scene creation, scene editing, GDScript validation, project inspection, and error inspection ([Godot Asset Library](https://godotengine.org/asset-library/asset/4767)).

Human rule: when the AI changes scenes, verify in the editor. Do not assume a scene tree is correct because the text diff looks plausible.

### Unity

Unity is powerful and widely documented. C# is model-friendly, and the asset ecosystem is huge. It is a good fit for mobile games, cross-platform games, commercial prototypes, and projects where existing Unity assets save time.

Unity can also be used for runtime AI. Unity describes Sentis as the engine for running models inside the Unity Runtime, while Unity’s AI tools are focused on editor and creation workflows ([Unity](https://unity.com/features/ai)). If your shipped game will run a model locally, decide that early because it affects platform support, performance, memory, and build size.

Use Unity when the ecosystem helps more than the complexity hurts.

### Unreal and UEFN

Use Unreal when visual fidelity, cinematic tools, Unreal assets, or UEFN matter. Unreal is not the simplest first-game engine, but it can be the right tool for a visual prototype or a high-end 3D project.

For UEFN and Fortnite creator projects, remember that you are building inside someone else’s platform. That can be a benefit because distribution and monetization paths exist, but it also means rules and payouts are not under your control.

### App builders and repo-first app development

App builders are useful when you need to quickly see an idea. They are especially good for landing pages, dashboards, CRUD apps, internal tools, admin panels, and early SaaS prototypes. They are weaker when the project needs careful architecture, complex permissions, custom realtime behavior, native mobile features, or long-term maintainability.

The rule is simple: the more valuable the app becomes, the more important Git becomes. If you cannot export, branch, review, test, and deploy outside the builder, you are taking on risk. That is why tools with GitHub workflows are preferable for projects you might keep.

## Control scope without killing the project

Scope control is not about being negative. It is about getting to a version that exists.

### Minimum shippable version

The minimum shippable version is the smallest version that delivers the main experience. It is not the smallest amount of code. It is the smallest coherent product.

For a game, it might be:

- One character.
- One level or arena.
- One main mechanic.
- One win or lose condition.
- One restart flow.
- One music loop or sound set.
- One way to share or download it.

For an app, it might be:

- One user type.
- One main screen.
- One primary action.
- One saved data object.
- One error state.
- One deployment target.

Accessibility and localization belong here if they matter to your audience. For example, “keyboard playable” is not polish if keyboard users are part of the audience. Externalizing text is not polish if the app may need translation. Put these requirements in the minimum shippable version early enough that the AI does not bake in bad assumptions.

### Cut lists

A cut list should help you make decisions under pressure.

- **Deferred for now**: Good ideas that you are intentionally not building yet.
- **Cut if blocked**: Features that disappear if they do not work inside a timebox.
- **Post-launch**: Features that would improve the project later but are not needed to release.

Example for a multiplayer browser game:

- Deferred for now: cosmetics, leaderboard, advanced matchmaking.
- Cut if blocked: mid-round joining, host migration, replay system.
- Post-launch: ranked mode, achievements, more maps.

Example for an app:

- Deferred for now: team workspaces, billing, native mobile app.
- Cut if blocked: realtime collaboration.
- Post-launch: integrations, templates, analytics dashboard.

Cutting is not failure. It is how you make room for the version that can actually ship.

### When to kill a prototype

AI makes it easy to start too many projects. That means you need a humane way to stop.

After a few focused sessions, ask:

- Is the core loop fun or promising?
- Is the app flow useful to a real person?
- Can I explain the next user test?
- Is the remaining work exciting enough to continue?
- Is there a smaller version worth saving?

If the answer is no, archive the prototype and keep the lesson. That is not failure. That is research.

## Multiplayer without hand-waving

Multiplayer is not one feature. It changes the architecture.

Before asking AI to build multiplayer, decide who is allowed to decide truth. In a simple local game, the player’s machine can decide everything. In a multiplayer game, that becomes dangerous because each client may disagree or cheat.

### Authority model

A safer default is host-authoritative or server-authoritative logic:

- Clients send inputs.
- The host or server assigns identity.
- The host or server decides positions, scores, round state, and winners.
- Clients render what the authority says happened.

Do not let each client decide who they are, whether they scored, whether they won, or whether they are the host.

### Round and phase bugs

Many multiplayer bugs happen during transitions. A round starts, someone disconnects, the host changes state, a late message arrives, and one client thinks the game is starting while another thinks it is ending.

Ask the AI to define:

- What phases exist: lobby, countdown, playing, round over, results.
- Who can change the phase.
- What happens if a message arrives late.
- What happens if the host leaves.
- What happens if someone refreshes.
- What happens if a player joins at the wrong time.

Prompt:

```text
Before implementing multiplayer, design the round-state flow.

Include:
- All phases.
- Who owns phase changes.
- What messages are sent between clients and host/server.
- What happens on disconnect.
- What happens if a late message arrives from a previous phase.
- Manual tests with two, three, and four clients.

Do not write code yet.
```

### Validation

Validation means checking whether a client’s claim is believable. If validation is too loose, cheating is easy. If it is too strict, normal lag or chaotic physics may get rejected. Tune it based on actual play, not imaginary constants.

Multiplayer requires real testing with multiple clients. Two tabs on your machine are a start, not the finish.

## Assets, audio, and provenance

AI can help with assets, but it is safest to think of AI assets as drafts until proven otherwise.

Use AI freely for:

- Moodboards.
- Placeholder art.
- Concept sketches.
- Texture ideas.
- UI variations.
- Dialogue drafts.
- Sound effect experiments.
- Temporary music.
- Trailer script drafts.
- Store description drafts.

Be more careful with final:

- Logos.
- Character designs.
- Voice likenesses.
- Music.
- Marketing art.
- Anything imitating a living artist, brand, celebrity, franchise, or copyrighted style.

### The asset log

Keep a simple asset log. It can be a spreadsheet or markdown table.

| Asset | Tool/source | Human edits | Final use | Risk notes |
| --- | --- | --- | --- | --- |
| Main character sketch | AI image tool | Redrawn and recolored | Concept only | Not shipped |
| Button click sound | Licensed pack | Trimmed | Shipped | License saved |
| Trailer voice | Voice tool | Script written by human | Marketing | Check commercial terms |

The point is not paranoia. The point is being able to answer “where did this come from?” when a store, publisher, collaborator, or future you asks.

### Copyright and human authorship

The U.S. Copyright Office says generative AI outputs can be protected only where a human author contributes sufficient expressive elements, such as human-authored material that remains perceptible or human creative arrangement or modification of the output, and prompts alone are not enough ([U.S. Copyright Office](https://www.copyright.gov/newsnet/2025/1060.html)).

Practical meaning:

- Do not assume raw AI output is protectable.
- Add human authorship through editing, arranging, redrawing, rewriting, composing, and integrating.
- Keep records of what the human changed.
- Separate internal drafts from shipped assets.

## Legal, platform, and safety basics

This section is not legal advice. It is the practical checklist that keeps AI projects from drifting into obvious risk.

### Steam AI disclosure

Steam’s Content Survey asks about generative AI when a game used AI services during development or includes AI services in the product. Steam defines pre-generated AI as content such as art, code, or sound created with AI tools during development, and live-generated AI as content created while the game is running ([Steamworks Documentation](https://partner.steamgames.com/doc/gettingstarted/contentsurvey)).

The practical distinction:

- A coding assistant or concept-art draft is development usage.
- AI-generated art, sound, dialogue, code, or marketing material that ships is shipped content.
- An AI NPC, live text generator, live image generator, or model-driven user feature is live AI.

Live AI needs guardrails. Steam says developers using live-generated AI must describe guardrails that prevent illegal content, and external AI service costs must be handled through Steam-supported payment methods such as base price, microtransactions, subscriptions, or DLC ([Steamworks Documentation](https://partner.steamgames.com/doc/gettingstarted/contentsurvey)).

### App stores and payments

Do not add payments at the last minute. Apple’s App Store Review Guidelines say apps unlocking digital features or functionality, including subscriptions, in-game currencies, game levels, premium content, or full versions, must use in-app purchase unless an exception applies ([Apple Developer](https://developer.apple.com/app-store/review/guidelines/)). Apple’s App Store Connect docs explain that in-app purchases need configuration, StoreKit implementation, metadata, tax setup, and review before publication ([Apple Developer](https://developer.apple.com/help/app-store-connect/configure-in-app-purchase-settings/overview-for-configuring-in-app-purchases/)). Google Play subscriptions need an active base plan before users can buy them ([Google Help](https://support.google.com/googleplay/android-developer/answer/140504?hl=en)).

### AI security in plain English

If users can type into an AI feature, upload files, interact with AI characters, or trigger AI tools, treat the feature like an exposed system.

The basic risks:

- Users may try to trick the model.
- The model may reveal hidden instructions.
- The model may output unsafe text, HTML, code, or commands.
- A tool-using agent may take actions it should not take.
- Live AI may cost more money than expected.
- Generated content may need moderation.

OWASP’s LLM Top 10 covers generative AI application risks, while its Agentic Applications Top 10 focuses on systems where AI agents plan, act, and make decisions through tools ([OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/?cat=44), [OWASP Agentic Applications Top 10](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)).

Practical safety rules:

- Keep secrets out of prompts.
- Treat user input as untrusted.
- Do not let models directly run destructive actions.
- Require approval for purchases, public posts, deletes, account changes, or moderation actions.
- Validate AI output before showing it to users or passing it into code.
- Rate-limit live AI.
- Log enough to debug abuse and failures.
- Provide a fallback when the model is slow or down.

## Publishing and monetization

Publishing depends on the kind of project.

### Web games and web apps

Use web hosting when you want people to try the project quickly. Vercel, Netlify, Cloudflare Pages, GitHub Pages, Replit, and Firebase App Hosting can all make sense depending on the stack. The important question is not “which host is coolest?” It is:

- Can I deploy repeatedly?
- Can I roll back?
- Can I verify the deployed version?
- Can I connect the project to Git?
- Can I keep secrets out of the client?
- Can I support the backend features I need?

Vercel v0 is especially relevant for AI-assisted app builders because it connects generation to GitHub imports, branches, pull requests, previews, deployments, and security controls ([Vercel](https://vercel.com/blog/introducing-the-new-v0)). Lovable and Bolt are useful when you want fast app generation, but the important habit is to connect or export to Git before the generated project becomes valuable ([Lovable Docs](https://docs.lovable.dev/integrations/github), [Bolt Support](https://support.bolt.new/integrations/git)).

### itch.io

Use itch.io for game jams, prototypes, browser games, pay-what-you-want releases, and early community feedback. It is friendly to small and strange games. Itch.io says sellers can choose open revenue sharing from 0% to 100%, with 10% as the default platform share ([itch.io](https://itch.io/docs/creators/payments)).

### Steam

Use Steam when the game is commercial enough to justify store setup, capsule art, trailers, reviews, community support, patches, content surveys, and AI disclosure records. Start the Steam path early if the game uses AI-generated assets, live AI, mature content, external AI service costs, or multiplayer.

### Mobile stores

Use mobile stores when the project depends on native phone behavior: notifications, camera, sensors, location, offline installs, app-store trust, or mobile payments. Expo can help with build submission, but app review, screenshots, privacy labels, payment rules, device QA, and support still remain your responsibility ([Expo](https://docs.expo.dev/deploy/submit-to-app-stores/), [Apple Developer](https://developer.apple.com/app-store/review/guidelines/)).

### After launch

You can use AI after launch, but you do not have to. It is useful when it saves time without taking over judgment.

AI can help:

- Summarize reviews.
- Cluster bug reports.
- Draft changelogs.
- Draft devlogs.
- Turn playtest notes into issue lists.
- Draft store copy variants.
- Create trailer script ideas.
- Transcribe playtest recordings.

Do not let AI decide what matters most. Use it to organize feedback so you can make better human decisions.

## Recommended workflows

Use these as starting paths. They are not laws, but they are good defaults when a blank page feels overwhelming.

### First game workflow

Use this if you are new and want to finish something.

1. Pick Phaser, Godot 2D, or plain web.
2. Write a one-paragraph concept.
3. Write the project contract.
4. Create an empty project that runs.
5. Add one player action.
6. Add one response to that action.
7. Add a score, timer, win state, lose state, or restart.
8. Ask one person to play.
9. Cut anything that does not support the core loop.
10. Share it on itch.io or as a web link.

Avoid online multiplayer, procedural worlds, large RPG systems, real-money economies, and live AI characters for the first project.

### Polished 2D game workflow

1. Choose Godot or Phaser.
2. Prototype the core mechanic with placeholder art.
3. Create one complete level, room, or encounter.
4. Test the feel with a real person.
5. Add only the UI needed to play.
6. Add save/load only if the game needs it.
7. Replace placeholder assets gradually.
8. Run performance checks.
9. Prepare store or itch page.
10. Keep a post-launch list instead of adding everything before release.

### 3D or cinematic prototype workflow

1. Choose Godot, Unity, or Unreal based on fidelity and platform.
2. Greybox the scene before polishing assets.
3. Define camera behavior early.
4. Add movement or interaction.
5. Add one objective or cinematic beat.
6. Check performance on target hardware.
7. Lock the asset pipeline.
8. Only then add polish, lighting, VFX, or animation passes.

If the goal is a cinematic prototype, do not let the AI overbuild game systems that do not support the shot, mood, or proof.

### Web app or SaaS prototype workflow

1. Define one user and one main task.
2. Choose an app builder or repo-first stack.
3. Generate or build the first screen.
4. Connect to Git before the app becomes valuable.
5. Add one data model.
6. Add the primary action.
7. Add basic error states.
8. Deploy a preview.
9. Watch someone use it.
10. Move to repo-based development before auth, payments, teams, permissions, or migrations become complex.

### AI-native workflow

1. Define what the AI feature actually does for the user.
2. Decide what input reaches the model.
3. Decide what output reaches users.
4. Decide what tools the model can use.
5. Add logging and rate limits.
6. Add moderation or safety checks where needed.
7. Add fallback behavior when AI is slow, wrong, or unavailable.
8. Track cost per user or per session.
9. Test prompt injection and unsafe output.
10. Check platform disclosure requirements before release.

## Prompt library

Prompts are not magic spells. They are little work orders. A good prompt gives the AI a role, context, end result, constraints, and verification method.

### Project definition

```text
Help me define this project before we build it.

Idea:
[describe the game or app]

Ask clarifying questions first.
Then produce:
- One-sentence concept.
- Target player/user.
- Target platform.
- Recommended stack.
- First shippable version.
- Cut list.
- Biggest risks.
- First 10 steps.

Do not write code yet.
```

### Tool and stack selection

```text
Help me choose the simplest stack that can ship this.

Project:
[describe]

Compare:
- Browser game stack.
- Godot.
- Unity.
- Unreal/UEFN.
- App builder.
- Repo-first web app.
- Mobile stack.

For each, explain:
- Why it fits.
- Why it might be a trap.
- How AI can help.
- What a beginner will struggle with.
- Deployment path.

Recommend one default and one fallback.
```

### Local workflow setup

```text
Help me set up an AI coding workflow for my computer.

My machine level:
[low-end / mid-tier / high-end]

My priorities:
[privacy / no inference cost / strongest model / simplest setup / offline work]

My project:
[game or app type]

Recommend:
- Cloud-first, local-first, or hybrid workflow.
- Tools to use.
- What local models should be used for.
- What cloud models should be used for, if any.
- How to keep context small.
- How to avoid letting the agent make broad risky changes.
```

### One-feature implementation

```text
Read the project contract first.

Task:
[one feature or bug]

End result:
[what the user/player should see, feel, or be able to do]

Acceptance criteria:
- [observable behavior]
- [observable behavior]
- [manual or automated check]

Constraints:
- Do not broaden scope.
- Do not change unrelated files.
- Use targeted edits.
- Show the plan before broad edits.
- After changes, tell me exactly how to verify.
```

### Visual or gameplay feel

```text
Improve this feature, but do not change the scope.

Current behavior:
[describe what happens now]

Desired feel:
[arcade / cozy / fast / heavy / readable / playful / tense]

Examples:
[optional references]

Do:
- Propose 3 small changes.
- Explain what each change affects.
- Implement only the one I approve.

Do not:
- Rewrite the whole system.
- Add new mechanics.
- Change unrelated UI.
```

### Bug fixing

```text
Fix this bug.

Observed behavior:
[what I see]

Expected behavior:
[what should happen]

Reproduction steps:
1. [step]
2. [step]
3. [step]

Rules:
- Treat the observed behavior as ground truth.
- Do not explain it away.
- Identify likely causes first.
- Propose the smallest diagnostic.
- Make one targeted fix.
- Tell me how to verify.
```

### Revert-first debugging

```text
This worked before the last change.

First:
- Identify the exact change that likely broke it.
- Explain whether reverting is safer than forward-fixing.
- Do not stack unrelated fixes.

Then:
- Propose the smallest safe next step.
```

### Production verification

```text
Verify the deployed product, not local files.

Change:
[describe]

Marker:
[unique marker]

Steps:
1. Confirm the change is committed and deployed.
2. Fetch the deployed URL with no-cache headers.
3. Search the remote response for the marker.
4. Test the behavior on the live URL.
5. Report exactly what proved it.

Local grep does not count.
```

### Multiplayer planning

```text
Plan multiplayer before writing code.

Game:
[describe]

Design:
- Authority model.
- Player identity.
- Lobby flow.
- Round phases.
- Disconnect handling.
- Host-leave behavior.
- Late message handling.
- What clients are allowed to decide.
- What the host/server decides.

Return a design and test plan.
Do not write code yet.
```

### AI safety review

```text
Review this AI feature before implementation.

Feature:
[describe]

Check:
- What user input reaches the model.
- What output reaches users.
- What tools the model can use.
- What actions need human approval.
- What could be abused.
- What gets logged.
- Rate limits and cost limits.
- Fallback behavior.
- Store/platform disclosure.

Return a minimal safe design before code.
```

### Pre-release review

```text
Act as a release reviewer.

Project:
[describe]

Check:
- Core loop or main flow.
- Build.
- Deployment.
- Performance.
- Accessibility basics.
- Asset/license records.
- AI disclosure.
- Payments, if any.
- Store page, if any.
- Rollback plan.
- Known cuts and post-launch list.

Return:
- Must fix before release.
- Should fix soon.
- Safe to ship.
```

## Checklists that actually help

Use these at the moment they become relevant. Do not try to complete every checklist on day one.

### Before building

- I know what kind of project this is.
- I know the target player or user.
- I know the target platform.
- I chose a stack for a reason.
- I wrote the project contract.
- I listed the first shippable version.
- I listed what I will cut or defer.

### Before asking AI to edit

- The AI has read the project contract.
- The task is one feature or one bug.
- I described the end result.
- I gave acceptance criteria.
- I told it what not to change.
- I know how I will verify the result.

### Before calling something done

- I ran the project.
- I tested the feature myself.
- I checked the target device, editor, or deployed URL if relevant.
- I read the diff or change summary.
- I know what changed, what is broken, and what comes next.

### Before release

- A clean checkout builds.
- The full main flow works.
- Performance is acceptable on the target hardware.
- Store or publishing requirements are understood.
- Payment or download flow is tested if relevant.
- Asset and license records exist.
- AI usage and disclosure records exist if relevant.
- There is a rollback plan.
- Post-launch features are not blocking release.

## What AI still will not do for you

AI is a powerful collaborator, but it is not the owner of the project. It will not reliably know whether the game is fun, whether the app is useful, whether the jump feels right, whether the camera makes people sick, whether the UI is clear, or whether the art direction holds together.

AI also does not naturally protect your scope. If you keep asking for more, it will keep adding more. That does not mean the project is getting better. Sometimes the most professional move is to stop adding, test what exists, and ship the smallest coherent version.

The point of using AI is not to remove the human from the process. The point is to spend less time fighting boilerplate, searching docs, writing repetitive code, summarizing feedback, and drafting first passes so you can spend more time making judgment calls. Define the target, constrain the agent, verify reality, cut what does not matter, and keep going until there is something real for people to use or play.

## License

This guide is licensed under the Creative Commons Attribution 4.0 International License. You may share and adapt it with attribution.
