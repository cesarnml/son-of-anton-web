<script lang="ts">
	const GITHUB = 'https://github.com/cesarnml/son-of-anton';
	const CODOGOTCHI = 'https://codogotchi.app';
	const INSTALL_CMD =
		'git subtree add --prefix .son-of-anton https://github.com/cesarnml/son-of-anton.git main --squash';

	// Gate names from tools/delivery/codogotchi-gate.ts — the actual events the
	// orchestrator writes to ~/.codogotchi/gate.json
	const gateEvents = [
		'ticket_started',
		'red_tdd',
		'green_tdd',
		'adversarial_review',
		'open_pr',
		'poll_review',
		'record_review',
		'review_clean',
		'ticket_completed'
	];

	const petFaces: Record<string, string> = {
		ticket_started: '(o_o)',
		red_tdd: '(>_<)',
		green_tdd: '(^_^)',
		adversarial_review: '(¬_¬)',
		open_pr: '(o_O)/',
		poll_review: '(._.)zZ',
		record_review: '(=_=)',
		review_clean: '\\(^o^)/',
		ticket_completed: '(^‿^)'
	};

	let gateIndex = $state(0);
	let copied = $state(false);

	$effect(() => {
		const id = setInterval(() => {
			gateIndex = (gateIndex + 1) % gateEvents.length;
		}, 2200);
		return () => clearInterval(id);
	});

	const currentGate = $derived(gateEvents[gateIndex]);

	async function copyInstall() {
		await navigator.clipboard.writeText(INSTALL_CMD);
		copied = true;
		setTimeout(() => (copied = false), 1800);
	}

	const gates = [
		{
			phase: 'GATE 01',
			title: 'Approve the WHAT',
			cmd: '/soa plan',
			body: 'A grill-me session forces the AI to surface assumptions, constraints, and scope decisions back to you before any ticket is written. You say yes or refine. The AI does not proceed until you have.'
		},
		{
			phase: 'GATE 02',
			title: 'Approve the HOW',
			cmd: '/soa decompose',
			body: 'The approved plan becomes a ticket stack — ordered, dependency-aware, sized for review. Architectural judgment belongs to you. Ticket authorship belongs to the agent.'
		},
		{
			phase: 'GATE 03',
			title: 'Approve DONE',
			cmd: '/soa closeout',
			body: 'An adversarial subagent reviews every ticket before its PR opens. When the phase is complete, you decide whether to accept. Closeout squash-merges the stack onto main. Nothing merges without you.'
		}
	];

	const deliverables = [
		{
			n: '01',
			title: 'Delivery orchestrator',
			body: 'A TypeScript CLI that drives the ticket loop, manages worktrees and branches, records review outcomes, and enforces stop conditions. Runs via Bun or Node.'
		},
		{
			n: '02',
			title: 'Skill layer',
			body: 'Behavioral instructions in .agents/skills/ that any agent can read, plus per-agent adapters for platforms with their own file conventions.'
		},
		{
			n: '03',
			title: 'Adversarial subagent review',
			body: 'After each ticket, a second AI pass checks the implementation assuming the first one cut corners. Every review leaves artifacts: prompt, report, and ledger. The reconcile step hard-blocks the PR if the ledger would silently disagree with git history.'
		},
		{
			n: '04',
			title: 'Stacked PR model',
			body: 'Each ticket gets its own branch and PR, stacked in dependency order. Closeout squash-merges the whole phase onto main cleanly.'
		},
		{
			n: '05',
			title: 'Migration runner',
			body: 'When Son of Anton ships structural changes, bun run sync applies them to your repo automatically. You pull and run; the migration runs itself.'
		},
		{
			n: '06',
			title: 'Agent-rule injection',
			body: 'Sync injects skill-trigger rules into AGENTS.md so every agent in your repo knows which skills to invoke and when. Idempotent — re-running is always safe.'
		}
	];

	const nots = [
		['Not a code generator.', 'It does not write boilerplate or scaffold projects.'],
		[
			'Not a fully autonomous agent.',
			'The three gates are real stops where a human decision is required. There is no "just ship it" mode.'
		],
		[
			'Not a cloud service.',
			'Everything runs locally. Your code never leaves your machine except where it already does — GitHub PRs and your AI provider.'
		],
		[
			'Not opinionated about your stack.',
			"TypeScript is the orchestrator's runtime; your application can be anything."
		],
		[
			'Not tied to one agent.',
			'Runs on Codex, Cursor, Copilot, Claude, OpenCode — anything that reads AGENTS.md. Swap agents mid-project if you want.'
		]
	];

	const pipeline = [
		'OPEN WORKTREE',
		'IMPLEMENT',
		'VERIFY',
		'ADVERSARIAL REVIEW',
		'OPEN PR',
		'POLL AI REVIEW',
		'TRIAGE',
		'NEXT TICKET'
	];
</script>

<svelte:head>
	<title>Son of Anton — A delivery orchestrator with human decision gates</title>
	<meta
		name="description"
		content="Son of Anton is a delivery orchestrator for solo developers and small teams. Three human gates — plan, decompose, closeout — and the orchestrator owns everything in between. Runs on Codex, Cursor, Copilot, Claude, or any agent that reads AGENTS.md."
	/>
	<meta property="og:title" content="Son of Anton" />
	<meta
		property="og:description"
		content="AI should do the implementation. You should own the decisions."
	/>
</svelte:head>

<div class="font-body antialiased">
	<!-- ============ NAV ============ -->
	<nav
		class="sticky top-0 z-50 flex w-full items-center justify-between border-b-2 border-carbon bg-paper px-5 py-3 md:px-10"
	>
		<a href="#top" class="font-mono text-xs font-bold tracking-[0.2em] uppercase">
			Son of Anton<span class="text-cobalt">_</span>
		</a>
		<div class="hidden items-center gap-1 font-mono text-sm md:flex">
			<a href="#gates" class="px-2 py-1 transition-colors hover:bg-cobalt hover:text-white"
				>GATES</a
			>
			<a href="#workflow" class="px-2 py-1 transition-colors hover:bg-cobalt hover:text-white"
				>WORKFLOW</a
			>
			<a href="#codogotchi" class="px-2 py-1 transition-colors hover:bg-cobalt hover:text-white"
				>CODOGOTCHI</a
			>
			<a href="#install" class="px-2 py-1 transition-colors hover:bg-cobalt hover:text-white"
				>INSTALL</a
			>
		</div>
		<div class="flex items-center gap-3">
			<a
				href={GITHUB}
				class="border-2 border-carbon bg-paper px-3 py-2 font-mono text-xs font-bold tracking-wider uppercase transition-colors hover:bg-carbon hover:text-paper"
			>
				GitHub ↗
			</a>
			<a
				href="#install"
				class="hidden border-2 border-cobalt bg-cobalt px-3 py-2 font-mono text-xs font-bold tracking-wider text-white uppercase transition-colors hover:bg-paper hover:text-cobalt sm:block"
			>
				/soa plan
			</a>
		</div>
	</nav>

	<main id="top">
		<!-- ============ HERO ============ -->
		<section class="w-full border-b-2 border-carbon px-5 pt-16 pb-20 md:px-10 md:pt-24 md:pb-28">
			<div class="grid grid-cols-1 gap-10 lg:grid-cols-12">
				<div class="flex flex-col justify-center lg:col-span-7">
					<p class="mb-6 font-mono text-xs font-bold tracking-[0.2em] text-cobalt uppercase">
						A delivery orchestrator for solo developers &amp; small teams
					</p>
					<h1
						class="font-display mb-8 text-5xl leading-[1.02] font-extrabold tracking-tight md:text-7xl"
					>
						AI should do the implementation.<br />
						<span class="text-cobalt">You should own the decisions.</span>
					</h1>
					<p
						class="mb-10 max-w-2xl border-l-4 border-cobalt pl-5 text-lg leading-relaxed text-ink-muted"
					>
						The default for AI-assisted development is one of two failure modes: you're either
						babysitting the agent line by line, or you've handed it the wheel and are hoping for the
						best. Son of Anton is neither. There are exactly three moments where a developer's
						judgment is irreplaceable — the orchestrator owns everything in between.
					</p>
					<div class="flex flex-wrap gap-4">
						<a
							href="#install"
							class="border-2 border-cobalt bg-cobalt px-8 py-4 font-mono text-sm font-bold tracking-wider text-white uppercase transition-colors hover:bg-paper hover:text-cobalt"
						>
							Install via subtree
						</a>
						<a
							href="#gates"
							class="border-2 border-carbon bg-paper px-8 py-4 font-mono text-sm font-bold tracking-wider uppercase transition-colors hover:bg-carbon hover:text-paper"
						>
							See the gates
						</a>
					</div>
				</div>

				<!-- Terminal panel -->
				<div class="lg:col-span-5">
					<div class="border-2 border-carbon bg-carbon p-6 font-mono text-sm text-paper md:p-8">
						<div class="mb-5 flex items-center justify-between border-b border-ink-muted pb-3">
							<span class="text-xs tracking-[0.15em] text-amber">SOA_DELIVERY_LOOP</span>
							<span class="text-xs text-line-soft">state.json</span>
						</div>
						<div class="space-y-3 leading-relaxed">
							<p><span class="text-amber">$</span> /soa plan</p>
							<p class="pl-4 text-line-soft"># grill-me → approved plan</p>
							<p><span class="text-amber">$</span> /soa decompose docs/product/plans/phase-N.md</p>
							<p class="pl-4 text-line-soft"># ticket stack → you approve the list</p>
							<p><span class="text-amber">$</span> /soa execute phase-N</p>
							<p class="pl-4 text-line-soft"># orchestrator delivers ticket by ticket</p>
							<p><span class="text-amber">$</span> /soa closeout phase-N</p>
							<p class="pl-4 text-line-soft"># stacked PRs squash-merge to main</p>
							<p class="pt-2">
								<span class="text-amber">▮</span>
								<span class="bg-cobalt px-2 py-0.5 text-xs font-bold text-white"
									>HUMAN GATE: APPROVAL REQUIRED</span
								>
							</p>
						</div>
					</div>
					<div
						class="flex justify-between border-2 border-t-0 border-carbon bg-paper-bright px-4 py-2 font-mono text-xs"
					>
						<span>FIG. 01 — THE DELIVERY LOOP</span>
						<span class="text-cobalt">SYS_OP_ACTIVE</span>
					</div>
				</div>
			</div>
		</section>

		<!-- ============ THREE GATES ============ -->
		<section id="gates" class="w-full border-b-2 border-carbon">
			<div class="grid grid-cols-1 md:grid-cols-3">
				{#each gates as gate, i (gate.cmd)}
					<div
						class="group relative border-carbon p-8 transition-colors hover:bg-paper-bright md:p-12 {i <
						2
							? 'border-b-2 md:border-r-2 md:border-b-0'
							: ''}"
					>
						<div class="mb-5 font-mono text-xs font-bold tracking-[0.2em] text-cobalt uppercase">
							{gate.phase}
						</div>
						<h2 class="font-display mb-4 text-3xl font-bold tracking-tight md:text-4xl">
							{gate.title}
						</h2>
						<div class="mb-6 inline-block bg-carbon px-3 py-1.5 font-mono text-sm text-amber">
							{gate.cmd}
						</div>
						<p class="leading-relaxed text-ink-muted">{gate.body}</p>
						<div
							class="absolute top-0 right-0 h-3 w-3 bg-cobalt opacity-0 transition-opacity group-hover:opacity-100"
						></div>
					</div>
				{/each}
			</div>
		</section>

		<!-- ============ PIPELINE STRIP ============ -->
		<section
			class="hide-scrollbar w-full overflow-x-auto border-b-2 border-carbon bg-carbon px-5 py-6 whitespace-nowrap text-paper md:px-10"
		>
			<div class="flex min-w-max items-center gap-6 font-mono text-sm">
				<span class="bg-amber px-2 py-1 text-xs font-bold text-carbon"
					>BETWEEN THE GATES, YOU ARE NOT NEEDED:</span
				>
				{#each pipeline as step, i (step)}
					<span class={step === 'ADVERSARIAL REVIEW' ? 'text-amber' : ''}>{step}</span>
					{#if i < pipeline.length - 1}
						<span class="h-[2px] w-10 bg-paper opacity-40"></span>
					{/if}
				{/each}
				<span class="text-line-soft">↻</span>
			</div>
		</section>

		<!-- ============ WHAT YOU GET ============ -->
		<section class="w-full border-b-2 border-carbon px-5 py-20 md:px-10 md:py-28">
			<div class="grid grid-cols-1 gap-10 lg:grid-cols-12">
				<div class="lg:col-span-4">
					<h2 class="font-display mb-6 text-4xl font-bold tracking-tight uppercase md:text-5xl">
						What you get
					</h2>
					<p class="leading-relaxed text-ink-muted">
						Son of Anton ships as a git subtree — no npm package, no submodule, no external service.
						The files are real tracked files in your repo history that you can read, diff, and
						bisect.
					</p>
				</div>
				<div class="lg:col-span-8">
					<ul class="border-t-2 border-carbon">
						{#each deliverables as item (item.n)}
							<li class="grid grid-cols-12 gap-4 border-b border-line-soft py-6">
								<span class="col-span-2 font-mono text-sm font-bold text-cobalt sm:col-span-1"
									>{item.n}</span
								>
								<div class="col-span-10 sm:col-span-11">
									<h3 class="mb-1 font-mono text-sm font-bold tracking-wider uppercase">
										{item.title}
									</h3>
									<p class="text-sm leading-relaxed text-ink-muted">{item.body}</p>
								</div>
							</li>
						{/each}
					</ul>
				</div>
			</div>
		</section>

		<!-- ============ WORKFLOW / RUNTIME POLICY ============ -->
		<section id="workflow" class="w-full border-b-2 border-carbon px-5 py-20 md:px-10 md:py-28">
			<div class="grid grid-cols-1 gap-10 lg:grid-cols-12">
				<div class="lg:col-span-4">
					<h2 class="font-display mb-6 text-4xl font-bold tracking-tight uppercase md:text-5xl">
						Runtime policy, not config archaeology
					</h2>
					<p class="mb-5 leading-relaxed text-ink-muted">
						<span class="bg-paper-bright px-1 font-mono text-sm">orchestrator.config.json</span> is
						the durable repo default. One-off operational choices are explicit flags — no config
						edits, no commits. The resolved policy is written to
						<span class="bg-paper-bright px-1 font-mono text-sm">state.json</span> at the start of every
						run.
					</p>
					<p class="leading-relaxed text-ink-muted">
						If config and run policy diverge mid-phase, <span
							class="bg-paper-bright px-1 font-mono text-sm">/soa resume</span
						> refuses to continue silently and prints both policies with exact recovery commands.
					</p>
					<div class="mt-8 border-2 border-carbon">
						<div
							class="border-b-2 border-carbon bg-carbon px-4 py-2 font-mono text-xs tracking-[0.15em] text-paper"
						>
							BOUNDARY MODES
						</div>
						<div class="grid grid-cols-12 border-b border-line-soft px-4 py-3 font-mono text-sm">
							<span class="col-span-3 font-bold text-cobalt">cook</span>
							<span class="col-span-9 text-ink-muted"
								>advances immediately after each ticket merges</span
							>
						</div>
						<div class="grid grid-cols-12 px-4 py-3 font-mono text-sm">
							<span class="col-span-3 font-bold text-cobalt">gated</span>
							<span class="col-span-9 text-ink-muted"
								>stops after each advance and prints a resume prompt</span
							>
						</div>
					</div>
					<p class="mt-3 font-mono text-xs tracking-wider text-ink-muted uppercase">
						Start with gated until you trust the agent on your codebase.
					</p>
				</div>
				<div class="lg:col-span-7 lg:col-start-6">
					<div
						class="border-2 border-carbon bg-carbon p-6 font-mono text-sm leading-loose text-paper md:p-8"
					>
						<p class="mb-4 border-b border-ink-muted pb-3 text-xs tracking-[0.15em] text-amber">
							RUNTIME OVERRIDES — NO CONFIG FILE EDITS REQUIRED
						</p>
						<p><span class="text-amber">$</span> /soa execute phase-N --boundary-mode gated</p>
						<p>
							<span class="text-amber">$</span> /soa execute phase-N --subagent-review-policy disabled
						</p>
						<p><span class="text-amber">$</span> /soa execute phase-N --subagent claude-cli</p>
						<p><span class="text-amber">$</span> /soa resume phase-N --boundary-mode cook</p>
						<p><span class="text-amber">$</span> /soa resume phase-N --baseline orchestrator</p>
						<p><span class="text-amber">$</span> /soa resume phase-N --baseline run-policy</p>
						<p class="mt-4 border-t border-ink-muted pt-3 text-xs text-line-soft">
							Subagent runners: claude-cli · codex-cli · cursor-cli. The CLI tries the preferred
							runner first, then the others, and refuses to record "clean" when none actually
							complete.
						</p>
					</div>
				</div>
			</div>
		</section>

		<!-- ============ CODOGOTCHI ============ -->
		<section
			id="codogotchi"
			class="w-full border-b-2 border-carbon bg-paper-bright px-5 py-20 md:px-10 md:py-28"
		>
			<div class="grid grid-cols-1 gap-10 lg:grid-cols-12">
				<div class="flex flex-col justify-center lg:col-span-6">
					<p class="mb-5 font-mono text-xs font-bold tracking-[0.2em] text-cobalt uppercase">
						Integration — Codogotchi
					</p>
					<h2 class="font-display mb-6 text-4xl font-bold tracking-tight md:text-5xl">
						Your delivery pipeline has a pulse. Now it has a pet.
					</h2>
					<p class="mb-5 max-w-xl leading-relaxed text-ink-muted">
						Son of Anton integrates with
						<a href={CODOGOTCHI} class="font-medium text-cobalt underline underline-offset-4"
							>Codogotchi</a
						>, the macOS desktop companion that reacts to your AI coding agent in real time. At
						every stage of the ticket loop the orchestrator writes a gate event to
						<span class="bg-paper px-1 font-mono text-sm">~/.codogotchi/gate.json</span> — and your pet
						acts it out on your menu bar.
					</p>
					<p class="mb-8 max-w-xl leading-relaxed text-ink-muted">
						Red TDD, green TDD, adversarial review, PR opened, review clean: nine gate events, each
						with its own mood. Best-effort and local-only — a write failure never aborts a delivery
						command, and you can switch it off with one line of config.
					</p>
					<div class="flex flex-wrap gap-4">
						<a
							href={CODOGOTCHI}
							class="border-2 border-carbon bg-carbon px-6 py-3 font-mono text-sm font-bold tracking-wider text-paper uppercase transition-colors hover:bg-paper-bright hover:text-carbon"
						>
							Get Codogotchi ↗
						</a>
						<span class="self-center font-mono text-xs text-ink-muted"
							>opt out: codogotchi.enabled = false</span
						>
					</div>
				</div>

				<!-- Live gate.json readout -->
				<div class="lg:col-span-5 lg:col-start-8">
					<div class="border-2 border-carbon bg-carbon p-6 font-mono text-sm text-paper md:p-8">
						<div class="mb-5 flex items-center justify-between border-b border-ink-muted pb-3">
							<span class="text-xs tracking-[0.15em] text-amber">~/.codogotchi/gate.json</span>
							<span class="text-xs text-line-soft">TTL 180s</span>
						</div>
						<pre class="leading-relaxed whitespace-pre-wrap">{`{
  "gate": "`}<span class="bg-amber px-1 font-bold text-carbon">{currentGate}</span>{`",
  "plan_key": "phase-N",
  "ticket_id": "pN-04"
}`}</pre>
						<div class="mt-6 flex items-center justify-between border-t border-ink-muted pt-4">
							<span class="text-2xl text-amber" aria-hidden="true">{petFaces[currentGate]}</span>
							<span class="text-xs tracking-[0.15em] text-line-soft uppercase"
								>PET STATUS: REACTING</span
							>
						</div>
					</div>
					<div
						class="flex justify-between border-2 border-t-0 border-carbon bg-paper px-4 py-2 font-mono text-xs"
					>
						<span>FIG. 02 — GATE EVENT FEED</span>
						<span class="text-cobalt">{gateIndex + 1}/{gateEvents.length}</span>
					</div>
				</div>
			</div>
		</section>

		<!-- ============ AGENT COMPATIBILITY ============ -->
		<section class="w-full border-b-2 border-carbon px-5 py-12 md:px-10">
			<div class="flex flex-col items-start justify-between gap-6 md:flex-row md:items-center">
				<p class="font-mono text-xs font-bold tracking-[0.2em] uppercase">
					Runs on the agent you already use
				</p>
				<div class="flex flex-wrap gap-3 font-mono text-sm">
					{#each ['CODEX', 'CURSOR', 'COPILOT', 'CLAUDE', 'OPENCODE'] as agent (agent)}
						<span class="border-2 border-carbon px-3 py-1.5">{agent}</span>
					{/each}
					<span class="border-2 border-cobalt bg-cobalt px-3 py-1.5 text-white"
						>ANYTHING THAT READS AGENTS.md</span
					>
				</div>
			</div>
		</section>

		<!-- ============ WHAT IT IS NOT ============ -->
		<section class="w-full border-b-2 border-carbon px-5 py-20 md:px-10 md:py-28">
			<div class="grid grid-cols-1 gap-10 lg:grid-cols-12">
				<div class="lg:col-span-4">
					<h2 class="font-display text-4xl font-bold tracking-tight uppercase md:text-5xl">
						What Son of Anton is <span class="text-cobalt">not</span>
					</h2>
				</div>
				<div class="lg:col-span-7 lg:col-start-6">
					<ul class="border-t-2 border-carbon">
						{#each nots as [head, rest], i (head)}
							<li class="grid grid-cols-12 gap-4 border-b border-line-soft py-5">
								<span class="col-span-2 font-mono text-sm font-bold text-cobalt sm:col-span-1"
									>0{i + 1}</span
								>
								<p class="col-span-10 leading-relaxed sm:col-span-11">
									<span class="font-semibold">{head}</span>
									<span class="text-ink-muted"> {rest}</span>
								</p>
							</li>
						{/each}
					</ul>
				</div>
			</div>
		</section>

		<!-- ============ INSTALL ============ -->
		<section id="install" class="w-full border-b-2 border-carbon px-5 py-20 md:px-10 md:py-28">
			<div class="grid grid-cols-1 gap-10 lg:grid-cols-12">
				<div class="lg:col-span-4">
					<h2 class="font-display mb-6 text-4xl font-bold tracking-tight uppercase md:text-5xl">
						Install
					</h2>
					<p class="mb-5 leading-relaxed text-ink-muted">
						Two commands. The subtree embeds at <span class="bg-paper-bright px-1 font-mono text-sm"
							>.son-of-anton/</span
						> — no submodules, no external service, no install step that can break.
					</p>
					<p class="font-mono text-xs tracking-wider text-ink-muted uppercase">
						Requirements: GitHub repo + gh CLI · Bun or Node · any agent that reads AGENTS.md ·
						working lint, format &amp; test commands
					</p>
				</div>
				<div class="space-y-6 lg:col-span-8">
					<div class="border-2 border-carbon">
						<div
							class="flex items-center justify-between border-b-2 border-carbon bg-carbon px-4 py-2"
						>
							<span class="font-mono text-xs tracking-[0.15em] text-amber"
								>STEP 1 — EMBED THE SUBTREE</span
							>
							<button
								onclick={copyInstall}
								class="border border-paper px-2 py-1 font-mono text-xs text-paper transition-colors hover:bg-paper hover:text-carbon"
							>
								{copied ? 'COPIED ✓' : 'COPY'}
							</button>
						</div>
						<pre class="overflow-x-auto bg-carbon p-5 font-mono text-sm leading-relaxed text-paper"><span
								class="text-amber">$</span> {INSTALL_CMD}</pre>
					</div>
					<div class="border-2 border-carbon">
						<div
							class="border-b-2 border-carbon bg-carbon px-4 py-2 font-mono text-xs tracking-[0.15em] text-amber"
						>
							STEP 2 — SYNC
						</div>
						<pre class="overflow-x-auto bg-carbon p-5 font-mono text-sm leading-relaxed text-paper"><span
								class="text-amber">$</span> bash .son-of-anton/scripts/soa-sync.sh</pre>
						<p
							class="border-t-2 border-carbon bg-paper-bright px-5 py-4 text-sm leading-relaxed text-ink-muted"
						>
							Wires the skill layer for your agents, injects rules into AGENTS.md (and CLAUDE.md for
							Claude Code), creates the orchestrator symlinks, and runs any pending migrations.
						</p>
					</div>
					<div class="border-2 border-carbon">
						<div
							class="border-b-2 border-carbon bg-carbon px-4 py-2 font-mono text-xs tracking-[0.15em] text-amber"
						>
							STEP 3 — START
						</div>
						<pre class="overflow-x-auto bg-carbon p-5 font-mono text-sm leading-relaxed text-paper"><span
								class="text-amber">$</span> /soa plan      <span class="text-line-soft"># if you have a concrete idea</span>
<span class="text-amber">$</span> /soa ideate    <span class="text-line-soft"># if you need to shape the idea first</span></pre>
					</div>
				</div>
			</div>
		</section>

		<!-- ============ FINAL CTA ============ -->
		<section
			class="w-full border-b-2 border-carbon bg-cobalt px-5 py-20 text-white md:px-10 md:py-28"
		>
			<div class="mx-auto max-w-4xl text-center">
				<h2 class="font-display mb-10 text-5xl font-extrabold tracking-tight md:text-7xl">
					READY TO ORCHESTRATE?
				</h2>
				<div class="flex flex-col justify-center gap-4 sm:flex-row">
					<a
						href={GITHUB}
						class="border-2 border-carbon bg-carbon px-10 py-5 font-mono text-sm font-bold tracking-wider uppercase transition-colors hover:bg-white hover:text-carbon"
					>
						View on GitHub ↗
					</a>
					<a
						href="#install"
						class="border-2 border-white bg-transparent px-10 py-5 font-mono text-sm font-bold tracking-wider uppercase transition-colors hover:bg-white hover:text-cobalt"
					>
						Install via subtree
					</a>
				</div>
			</div>
		</section>
	</main>

	<!-- ============ FOOTER ============ -->
	<footer
		class="flex w-full flex-col items-start justify-between gap-6 bg-carbon px-5 py-8 text-paper md:flex-row md:items-center md:px-10"
	>
		<div class="font-mono text-xs text-line-soft">
			© {new Date().getFullYear()} SON OF ANTON. ALL RIGHTS REDACTED.
		</div>
		<div class="flex flex-wrap gap-6 font-mono text-xs">
			<a href={GITHUB} class="text-line-soft uppercase transition-colors hover:text-amber"
				>GitHub</a
			>
			<a href="{GITHUB}#install" class="text-line-soft uppercase transition-colors hover:text-amber"
				>Docs</a
			>
			<a href={CODOGOTCHI} class="text-line-soft uppercase transition-colors hover:text-amber"
				>Codogotchi</a
			>
			<a
				href="{GITHUB}/blob/main/LICENSE"
				class="text-line-soft uppercase transition-colors hover:text-amber">License</a
			>
		</div>
		<div class="font-mono text-xs font-bold tracking-[0.2em] text-cobalt">SOA_CORE</div>
	</footer>
</div>

<style>
	.hide-scrollbar {
		scrollbar-width: none;
	}
	.hide-scrollbar::-webkit-scrollbar {
		display: none;
	}
</style>
