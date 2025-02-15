<script context="module" lang="ts">
	export const prerender = true;

	export const load = createKitDocsLoader({
		sidebar: {
			// @ts-ignore
			'/': null,
			'/docs': '/docs'
		}
	});
</script>

<script lang="ts">
	import '@svelteness/kit-docs/client/polyfills/index.js';
	import '@svelteness/kit-docs/client/styles/fonts.css';
	import '../app.css';
	import '../vars.css';

	import { navigating, page } from '$app/stores';

	import {
		Button,
		createKitDocsLoader,
		createSidebarContext,
		KitDocs,
		KitDocsLayout,
		SocialLink,
		type MarkdownMeta,
		type ResolvedSidebarConfig
	} from '@svelteness/kit-docs';
	import NProgress from 'nprogress';
	import 'nprogress/nprogress.css';
	import { config, navbar } from '../config';

	import '@docsearch/css/dist/style.css';
	// @ts-ignore
	import { Algolia } from '@svelteness/kit-docs/client/algolia';
	import '@svelteness/kit-docs/client/styles/docsearch.css';

	export let meta: MarkdownMeta | null = null;
	export let sidebar: ResolvedSidebarConfig | null = null;

	NProgress.configure({
		// Full list: https://github.com/rstacruz/nprogress#configuration
		minimum: 0.16,
		easing: 'ease',
		showSpinner: false
	});

	$: {
		// Prevent loading spinner when scrolling down
		if ($navigating?.from.pathname !== $navigating?.to.pathname) {
			NProgress.start();
		}
		if (!$navigating) {
			NProgress.done();
		}
	}

	const { activeCategory } = createSidebarContext(sidebar);

	$: category = $activeCategory ? `${$activeCategory}: ` : '';
	$: title = meta ? `${category}${meta.title} | FastEndpoints` : null;
	// @ts-ignore
	$: description = meta?.description;
</script>

<svelte:head>
	{#key $page.url.pathname}
		{#if title}
			<title>{title}</title>
		{/if}
		<meta name="description" content={config.seo.description} />
		<meta name="keywords" content={config.seo.keywords.join(', ')} />
		<!-- OG -->
		<meta property="og:url" content={`${config.siteUrl}${$page.url.pathname}`} />
		<meta property="og:type" content={config.openGraph.type} />
		<meta property="og:site_name" content={config.openGraph.siteName} />
		<meta property="og:description" content={config.openGraph.description} />
		<meta property="og:title" content={config.openGraph.title} />
		<meta property="og:locale" content={config.openGraph.locale} />
		<!-- Robots -->
		<!-- <meta name="robots" content="index,follow" /> -->
		<!-- <meta name="googlebot" content="index,follow" /> -->
	{/key}
</svelte:head>

<KitDocs {meta}>
	<KitDocsLayout {navbar} {sidebar} search>
		<Algolia
			apiKey={config.algolia.apiKey}
			appId={config.algolia.appId}
			indexName="docsearch"
			placeholder="Search documentation"
			slot="search"
		/>
		<div class="logo" slot="navbar-left">
			<Button href="/">
				<img src={'/logo.png'} alt="FastEndpoints logo" />
			</Button>
		</div>

		<div class="socials flex flex-row" slot="navbar-right-alt">
			<SocialLink type="gitHub" href={config.github} />
			<SocialLink type="discord" href={config.discord} />
		</div>

		<slot />

		<!-- <div slot="main-bottom" class="footer">
			<footer
				class="992:mt-10 mt-10 flex w-full flex-col items-center justify-center border-t border-gray-200 bg-gray-100 py-12 text-center text-base font-medium dark:border-gray-600 dark:bg-gray-700"
			>
				<span class="text-gray-soft mt-8">
					© FastEndpoints {new Date().getFullYear()}
				</span>
			</footer>
		</div> -->
	</KitDocsLayout>
</KitDocs>

<style>
	:global(:root) {
		--kd-color-brand-rgb: 0, 223, 255;
	}

	:global(:root.dark) {
		--kd-color-brand-rgb: 0, 223, 255;
	}

	.logo img :global {
		width: 185px;
	}

	.logo :global(a) {
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.logo :global(svg) {
		height: 36px;
		overflow: hidden;
	}
</style>
