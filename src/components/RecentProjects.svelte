<script lang="ts">
	interface GithubAPIResponse {
		name: string;
		fork: boolean;
		description: string;
		language: string;
		html_url: string;
		pushed_at: string;
	}

	let result: GithubAPIResponse[] = [];
	let delayOver = false;

	(async () => {
		const res = await fetch("https://cors.dsns.dev/api.github.com/users/dsnsgithub/repos");
		const data = (await res.json()) as GithubAPIResponse[];
		result = data
			.filter((repo) => !repo.fork)
			.sort((a, b) => new Date(b.pushed_at).getTime() - new Date(a.pushed_at).getTime())
			.slice(0, 10);
	})();

	setTimeout(() => {
		delayOver = true;
	}, 500);
</script>

{#if !delayOver || !result.length}
	<div class="m-4 grid gap-6 lg:grid-cols-2">
		{#each Array(10) as _, i}
			<div class="h-40 rounded-xl bg-viola-50 p-8">
				<div class="flex flex-row justify-between gap-4">
					<div class="basis-3/4">
						<div class="flex justify-between">
							<div class="h-6 w-32 animate-pulse rounded-xl bg-viola-200" />
						</div>
						<div class="mt-4 h-16 animate-pulse rounded-xl bg-viola-100" />
					</div>
					<div class="h-6 w-16 animate-pulse rounded-xl bg-viola-200" />
				</div>
			</div>
		{/each}
	</div>
{:else}
	<div class="m-4 grid gap-6 lg:grid-cols-2">
		{#each result as repo}
			<div class="rounded-xl bg-viola-50 p-8 shadow-md transition duration-500 hover:scale-[1.01]">
				<a href={repo.html_url} target="_blank" rel="noopener noreferrer" class="block">
					<div class="flex flex-row justify-between gap-10">
						<div class="flex flex-col gap-1">
							<div class="flex justify-between">
								<p class="text-xl font-bold">{repo.name}</p>
							</div>
							<div>
								{#if repo.description}
									<p>{repo.description}</p>
								{/if}
							</div>
							<div>
								<p title={new Date(repo.pushed_at).toLocaleString()}>
									Last updated: {new Date(repo.pushed_at).toLocaleDateString()}
								</p>
							</div>
						</div>
						<div>
							{#if repo.language}
								<div class="rounded-xl bg-viola-200 p-2">{repo.language}</div>
							{/if}
						</div>
					</div>
				</a>
			</div>
		{/each}
	</div>
{/if}
