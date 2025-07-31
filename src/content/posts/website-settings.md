---
title: 基础设置
published: 2025-07-31
description: '记录一下博客改造的内容'
image: ''
tags: ['Astro', 'Fuwari', '博客改造']
category: '教程'
draft: false 
lang: ''
series: '博客改造'
---


## 基础站点配置

> 保存设置的文件位置在`src`目录下的`config.ts`文件中

网站基础设置

```ts
export const siteConfig: SiteConfig = {
	title: "云弈斋",
	subtitle: "Blog",
	lang: "zh_CN", // 网站的默认语言，支持'en', 'zh_CN', 'zh_TW', 'ja', 'ko', 'es', 'th', 'vi'
	themeColor: {
		hue: 250, // 网站默认主题色, 范围是0 - 360
		fixed: false, // 是否隐藏网页的主题色选择器，false为显示。当不知道选什么颜色可以先在网页中手动调一下试试。
	},
	banner: {
		enable: true,
		src: "https://t.alcy.cc/pc", // 网站头图，使用相对地址也就是最开始不是斜杠的话默认是src目录下的内容，/加地址可以访问public目录下对应的内容，我这里使用的是随机在线图片
		position: "center", // 头图位置，默认是居中 支持'top', 'center', 'bottom'
		credit: {
			enable: false, // 是否显示头图的版权信息
			text: "", // 版权信息的具体内容
			url: "", // 一个指向原始作品或者作者的url，也可以不用输入，这不是必选项
		},
	},
	toc: {
		enable: true, // 是否在文章的右侧显示文章的大纲
		depth: 2, // 大纲的最大深度，如果是2的话就是最多显示到二级标题
	},
	favicon: [
		// 如果这个数组是空的就会使用默认的图标，如果有多个图标可以添加多个元素，网站会根据你的内容进行对应的适配
		{
			src: "/favicon/favicon.ico", // 图标路径，默认是public目录下的内容
			// theme: "light", // 可选 支持'light', 'dark' 对应的是网站当前的主题是亮色还是暗色，可以对不同情况进行适配
			// sizes: "32x32", // 可选 图标的大小，会自动请求对应大小的图标
		},
	],
};
```

顶部导航栏

> LinkPreset是一个枚举类型，如果是内部页面的导航建议在LinkPreset中添加并增加对应的i18n，例如友链等内容

```ts
export const navBarConfig: NavBarConfig = {
	links: [
		LinkPreset.Home,	// 首页
		LinkPreset.Archive,	// 归档页
		LinkPreset.About,	// 关于页
		{
			name: "统计",
			url: "https://umami.gabrlie.cn/share/aYsGHqFzCOLYe7IQ/astro.gabrlie.cn", // 自定义链接的地址
			external: true, // 点击后是否打开一个新标签页来显示，false就会直接在当前页面进行跳转
		},
	],
};
```

侧边栏

```ts
export const profileConfig: ProfileConfig = {
	avatar: "https://avatars.githubusercontent.com/u/57052263?v=4", // 头像地址，规则与banner的头图地址规则一样，相对地址为src目录，绝对地址是public目录，链接除外
	name: "Gabrlie",	// 名称
	bio: "Code create the world.",	// 简介
	links: [	// 你的社交地址，可以输入邮件，github，b站账户等内容
		{
			name: "GitHub",	// 名称
			icon: "fa6-brands:github",	// 图标，更多图标在 https://icon-sets.iconify.design
			url: "https://github.com/gabrlie",	// 跳转地址
		},
	],
};
```

版权声明

> 不知道这个做什么用的默认就行了，有需求的可以自己修改

```ts
export const licenseConfig: LicenseConfig = {
	enable: true,
	name: "CC BY-NC-SA 4.0",
	url: "https://creativecommons.org/licenses/by-nc-sa/4.0/",
};
```

代码块样式

> fuwari主题使用了Expressive Code增强代码块 相关文档 [Expressive Code](https://expressive-code.com/installation/)

```ts
export const expressiveCodeConfig: ExpressiveCodeConfig = {
	// Note: Some styles (such as background color) are being overridden, see the astro.config.mjs file.
	// Please select a dark theme, as this blog theme currently only supports dark background color
	theme: "github-dark",
};
```

## 页脚配置

> 页脚的内容在`src/components/Footer.astro`中，不仅可以修改信息与添加备案信息，也可以添加其它代码例如站点统计。`.astro`文件类似于tsx与vue，具体的语法可以前往astro的官方文档查看。

```astro
---

import { profileConfig } from "../config";

const currentYear = new Date().getFullYear();
---

<div class="transition border-t border-black/10 dark:border-white/15 my-10 border-dashed mx-32"></div>

<div class="transition border-dashed border-[oklch(85%_0.01_var(--hue))] dark:border-white/15 rounded-2xl mb-12 flex flex-col items-center justify-center px-6">
    <div class="transition text-50 text-sm text-center flex flex-wrap justify-center items-center gap-2">
        <!-- 版权信息 -->
        <div class="github-badge">
            <span class="badge-subject">Copyright</span>
            <span class="badge-value bg-blue">©{currentYear} {profileConfig.name}</span>
        </div>
        
        <!-- 技术支持 -->
        <div class="github-badge">
            <span class="badge-subject">Powered by</span>
            <a href="https://astro.build" target="_blank" class="badge-link">
                <span class="badge-value bg-green">Astro</span>
            </a>
        </div>
        
        <!-- 主题信息 -->
        <div class="github-badge">
            <span class="badge-subject">Theme</span>
            <a href="https://github.com/saicaca/fuwari" target="_blank" class="badge-link">
                <span class="badge-value bg-orange">Fuwari</span>
            </a>
        </div>
    </div>

    <style>
        /* 徽章样式 */
        .github-badge {
            display: inline-block;
            border-radius: 4px;
            text-shadow: none;
            font-size: 12px;
            color: #fff;
            line-height: 15px;
            background-color: #abbac3;
            margin: 2px;
            overflow: hidden;
        }
        
        .badge-subject {
            display: inline-block;
            background-color: #4d4d4d;
            padding: 4px 6px;
            border-top-left-radius: 4px;
            border-bottom-left-radius: 4px;
        }
        
        .badge-value {
            display: inline-block;
            padding: 4px 6px;
            border-top-right-radius: 4px;
            border-bottom-right-radius: 4px;
        }
        
        .badge-link {
            text-decoration: none;
            color: inherit;
            display: inline-block;
        }
        
        .bg-blue { background-color: #007ec6 }
        .bg-orange { background-color: #ffa500 }
        .bg-green { background-color: #3bca6e }
        
        /* 暗色模式适配 */
        @media (prefers-color-scheme: dark) {
            .github-badge {
                background-color: #3a4a55;
            }
            .badge-subject {
                background-color: #2d2d2d;
            }
        }
    </style>
</div>

<script defer src="https://umami.gabrlie.top/script.js" data-website-id="dd2cf105-7f2b-4a4b-b9ec-f7341431bd97"></script>
```

## 单个文章配置

> 这个内容在每个文章的最上方，使用`pnpm new-post <filename>`创建新文章后会自动添加对应模板，文章的存放位置为`src/content/posts/`中

```markdown
---
title: 				<!-- 标题，默认这里会填入创建时的文章名称 -->
published: 			<!-- 发布日期，创建时会自动填入当前时间 -->
description: ''		<!-- 简介 -->
image: ''			<!-- 封面 -->
tags: []			<!-- 标签，多个用逗号隔开 -->
category: ''		<!-- 分类，会自动创建对应分类 -->
draft: false 		<!-- 是否为草稿，草稿只有开发模式下才可见 -->
lang: ''			<!-- 语言，默认使用基础设置中的语言设置 -->
---
```