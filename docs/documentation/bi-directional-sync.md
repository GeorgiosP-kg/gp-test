---
title: Bi-Directional Sync
excerpt: >-
  This page will help you get started with Bi-Directional sync
hidden: false
---

<article class="rm-Article "><header id="content-head"><div class="row clearfix"><div class="col-xs-9"><h1>Bi-Directional Sync</h1></div></div></header><div class="grid-container-fluid" id="content-container"><section class="content-body grid-75"><div class="rm-Markdown markdown-body rm-Markdown markdown-body ng-non-bindable" data-testid="RDMD" style="margin-left: 31px;">

<blockquote class="callout callout_info" theme="📘"><h3 class="callout-heading"><span class="callout-icon">📘</span><p>Beta Feature</p></h3><p>We're excited to announce that bi-directional sync is currently in beta and will be gradually available to our ReadMe customers! If you’d like to be among the first to try it out, feel free to join our waitlist in the app. We can’t wait to have you on board!</p></blockquote>
<h1 class="heading heading-1 header-scroll"><div class="heading-anchor anchor waypoint" id="overview"></div><div class="heading-text">Overview</div><a aria-label="Skip link to Overview" class="heading-anchor-icon fa fa-regular fa-anchor" href="#overview"></a></h1>
<p>Our bi-directional sync feature allows you to effortlessly connect your documentation in ReadMe with your Git repositories, empowering your team to work where they thrive. Whether you prefer the intuitive ReadMe editor or your favorite Git workflow, this powerful integration ensures that your documentation is always up-to-date and easily accessible.</p>
<h2 class="heading heading-2 header-scroll"><div class="heading-anchor anchor waypoint" id="what-is-bi-directional-sync"></div><div class="heading-text">What is Bi-Directional Sync?</div><a aria-label="Skip link to What is Bi-Directional Sync?" class="heading-anchor-icon fa fa-regular fa-anchor" href="#what-is-bi-directional-sync"></a></h2>
<p>Bi-directional sync creates a two-way connection between your ReadMe project and a Git repository. When you make changes in either location:</p>
<ul>
<li>Changes made in ReadMe are automatically synced to your Git repository.</li>
<li>Changes pushed to your Git repository are automatically reflected in ReadMe.</li>
<li>Content stays consistent across both platforms without manual copying or updating.</li>
</ul>
<h2 class="heading heading-2 header-scroll"><div class="heading-anchor anchor waypoint" id="key-benefits"></div><div class="heading-text">Key Benefits</div><a aria-label="Skip link to Key Benefits" class="heading-anchor-icon fa fa-regular fa-anchor" href="#key-benefits"></a></h2>
<ul>
<li><strong>Write Where You Want</strong>: Give your team the flexibility to work in their preferred environment - whether that's ReadMe's editor UI or their local development setup.</li>
<li><strong>Version Control</strong>: Leverage Git's powerful version control capabilities for your documentation.</li>
<li><strong>Automated Syncing</strong>: Changes sync automatically between platforms, eliminating manual updates.</li>
<li><strong>Collaboration</strong>: Enable developers, engineers, and technical writers to collaborate seamlessly using familiar tools</li>
<li><strong>Single Source of Truth</strong>: Maintain consistency by having documentation synced across platforms.</li>
</ul>
<h2 class="heading heading-2 header-scroll"><div class="heading-anchor anchor waypoint" id="how-it-works"></div><div class="heading-text">How It Works</div><a aria-label="Skip link to How It Works" class="heading-anchor-icon fa fa-regular fa-anchor" href="#how-it-works"></a></h2>
<ol>
<li><strong>Connection Setup</strong>: Install the <strong>ReadMe Sync</strong> GitHub App in your repository to establish the connection.</li>
<li><strong>File Structure</strong>: Documentation is organized in a standardized folder structure:
<pre><button aria-label="Copy Code" class="rdmd-code-copy fa"></button><code class="rdmd-code lang-undefined theme-undefined"><span class="cm-s-neo" data-testid="SyntaxHighlighter">📂 project
├── 📁 guides
├── 📁 recipes 
├── 📁 custompages
├── 📁 references
└── 📃 sidebar.yml</span></code></pre>
</li>
<li><strong>Content Format</strong>:
<ul>
<li>Documentation pages are stored as Markdown files with frontmatter metadata.</li>
<li>Navigation and page order are managed through <code class="rdmd-code lang-undefined theme-undefined"><span class="cm-s-neo" data-testid="SyntaxHighlighter">sidebar.yml</span></code> files.</li>
<li>Content supports both standard Markdown and ReadMe's enhanced features.</li>
</ul>
</li>
</ol>
<h2 class="heading heading-2 header-scroll"><div class="heading-anchor anchor waypoint" id="git-integration-details"></div><div class="heading-text">Git Integration Details</div><a aria-label="Skip link to Git Integration Details" class="heading-anchor-icon fa fa-regular fa-anchor" href="#git-integration-details"></a></h2>
<p>ReadMe integrates through <a target="_self" href="https://docs.github.com/en/apps/overview">GitHub Apps</a> to provide:</p>
<ul>
<li>Easy installation process</li>
<li>Transparent permission management</li>
<li>Granular repository access controls</li>
<li>Ability to modify or revoke access at any time</li>
</ul>
<h3 class="heading heading-3 header-scroll"><div class="heading-anchor anchor waypoint" id="required-permissions"></div><div class="heading-text">Required Permissions</div><a aria-label="Skip link to Required Permissions" class="heading-anchor-icon fa fa-regular fa-anchor" href="#required-permissions"></a></h3>
<p>Repository-level access:</p>
<ul>
<li><strong>Metadata</strong> (Read-only): Required for basic GitHub functionality</li>
<li><strong>Contents</strong> (Read &amp; write): Used to sync documentation content</li>
</ul>
<p>The integration uses webhooks to:</p>
<ul>
<li>Detect changes in either platform</li>
<li>Trigger sync operations</li>
<li>Maintain content consistency</li>
<li>Handle conflict resolution</li>
</ul>
<h2 class="heading heading-2 header-scroll"><div class="heading-anchor anchor waypoint" id="versioning-support"></div><div class="heading-text">Versioning Support</div><a aria-label="Skip link to Versioning Support" class="heading-anchor-icon fa fa-regular fa-anchor" href="#versioning-support"></a></h2>
<p>Documentation versions are handled through Git branches:</p>
<ul>
<li>Default branch represents your main documentation version</li>
<li>Additional branches can be created for other versions</li>
<li>Version metadata (public/deprecated status) is managed in ReadMe</li>
</ul>
<h2 class="heading heading-2 header-scroll"><div class="heading-anchor anchor waypoint" id="getting-started"></div><div class="heading-text">Getting Started</div><a aria-label="Skip link to Getting Started" class="heading-anchor-icon fa fa-regular fa-anchor" href="#getting-started"></a></h2>
<p>To begin using bi-directional sync:</p>
<ol>
<li>Create a new Git repository for your documentation.</li>
<li>Install the <strong>ReadMe Sync</strong> GitHub App.</li>
<li>Connect your ReadMe project to the repository.</li>
<li>Start writing documentation in either platform.</li>
</ol>
<p>For more detailed information about working with bi-directional sync, see our "Editing with Bi-Directional Sync" guide.</p></div><div class="UpdatedAt"><p class="DateLine "><i class="icon icon-watch"></i>Updated 5 days ago </p></div><hr class="NextStepsDivider"><nav aria-label="Pagination Controls" class="PaginationControlsjDYuqu8pBMUy rm-Pagination"><a class="PaginationControls-link254uPEbaP92i PaginationControls-link_left1G58BmNHDbVm" aria-label="Previous Page: 🧩 Building Custom MDX Components" href="/main/docs/building-custom-mdx-components"><span class="PaginationControls-iconZbaZ3tgyhNmI icon-arrow-left2"></span><div class="PaginationControls-text3qEXHZU00znD PaginationControls-text_left3wBkn4C9R0W_">🧩 Building Custom MDX Components</div></a><a class="PaginationControls-link254uPEbaP92i PaginationControls-link_right5lsfuICZqXYk" aria-label="Next Page: Editing with Bi-Directional Sync" href="/main/docs/editing-with-bi-directional-sync"><div class="PaginationControls-text3qEXHZU00znD PaginationControls-text_right3EIfWubgRVVP">Editing with Bi-Directional Sync</div><span class="PaginationControls-iconZbaZ3tgyhNmI icon-arrow-right2"></span></a></nav><div class="rm-PageThumbs PageThumbs"><div class="PageThumbs-helpful">Did this page help you?<div class="PageThumbs-cta"><div class="Dropdown Dropdown_closed"><button aria-haspopup="dialog" class="Button Button_sm PageThumbs-button Dropdown-toggle Button_secondary Button_secondary_text" type="button" aria-expanded="false"><i class="icon icon-thumbs-up-2"></i><span>Yes</span></button></div><div class="Dropdown Dropdown_closed"><button aria-haspopup="dialog" class="Button Button_sm PageThumbs-button Dropdown-toggle Button_secondary Button_secondary_text" type="button" aria-expanded="false"><i class="icon icon-thumbs-down1"></i><span>No</span></button></div></div></div></div></section><section class="content-toc grid-25"><nav><ul class="toc-list"><li><a class="tocHeader" href="#"><i class="icon icon-text-align-left"></i>Table of Contents</a></li><li class="toc-children"><ul>
<li>
<a href="#overview">Overview</a>
</li>
<li>
<ul>
<li><a href="#what-is-bi-directional-sync">What is Bi-Directional Sync?</a></li>
<li><a href="#key-benefits">Key Benefits</a></li>
<li><a href="#how-it-works">How It Works</a></li>
<li><a href="#git-integration-details">Git Integration Details</a></li>
<li><a href="#versioning-support">Versioning Support</a></li>
<li><a href="#getting-started">Getting Started</a></li>
</ul>
</li>
</ul></li></ul></nav></section></div><div class="ModalWrapper" id="tutorialmodal-root"></div></article>
