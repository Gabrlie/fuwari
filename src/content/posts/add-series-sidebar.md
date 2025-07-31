---
title: 增加系列侧边栏
published: 2025-08-01
description: '系列侧边栏的功能是真的好用，也好看，就是设置有点麻烦'
image: ''
tags: ['Astro', 'Fuwari', '博客改造']
category: '教程'
draft: false 
lang: ''
series: '博客改造'
---
> 此方法来自于[伊卡的记事本](https://ikamusume7.org/posts/frontend/add_series_field/)，需要对博客框架进行小改造

## 增加多语言配置

> 这里如果不给每个翻译文件增加对应参数的话会产生报错，不过为了简洁这里只演示英文与中文的内容，其余语言可以通过翻译软件进行翻译

```ts ins={4}
<!-- src/i18n/i18nKey.ts -->
enum I18nKey {
    // ...
    series = 'series',
}
```

``` ts ins={4}
<!-- src/i18n/languages/en.ts -->
export const en: Translation = {
    // ...
    [Key.series]: 'Series',
}
```

``` ts ins={4}
<!-- src/i18n/languages/zh_CN.ts -->
export const en: Translation = {
    // ...
    [Key.series]: 'Series',
}
```

## 增加字段配置

```ts ins={4}
<!-- src/types/config.ts -->
export type BlogPostData = {
	// ...
	series?: string;
};
```

```ts ins={5}
<!-- src/content/config.ts -->
const postsCollection = defineCollection({
	schema: z.object({
		// ...
		series: z.string().optional(),
	}),
});
```

## 传入组件

```ts ins={7,12,17,24-26}
<!-- src\components\widget\SideBar.astro -->
---
import type { MarkdownHeading } from "astro";
import Categories from "./Categories.astro";
import Profile from "./Profile.astro";
import Tag from "./Tags.astro";
import Series from './Series.astro'

interface Props {
	class?: string;
	headings?: MarkdownHeading[];
    series?: string
}

const className = Astro.props.class;

const series = Astro.props.series
---
<div id="sidebar" class:list={[className, "w-full"]}>
    <div class="flex flex-col w-full gap-4 mb-4">
        <Profile></Profile>
    </div>
    <div id="sidebar-sticky" class="transition-all duration-700 flex flex-col w-full gap-4 top-4 sticky top-4">
        <div id="series" class="flex flex-col w-full gap-4">
            { series && <Series series={ series }></Series> }
        </div>
        <Categories class="onload-animation" style="animation-delay: 150ms"></Categories>
        <Tag class="onload-animation" style="animation-delay: 200ms"></Tag>
    </div>
</div>

```

```ts ins={9,"series"} 
<!-- src\layouts\MainGridLayout.astro -->
interface Props {
	title?: string;
	banner?: string;
	description?: string;
	lang?: string;
	setOGTypeArticle?: boolean;
	headings?: MarkdownHeading[];
    series?: string;
}

const {
	title,
	banner,
	description,
	lang,
	setOGTypeArticle,
	headings = [],
	series,
} = Astro.props;

// ...

<SideBar class="mb-4 row-start-2 row-end-3 col-span-2 lg:row-start-1 lg:row-end-2 lg:col-span-1 lg:max-w-[17.5rem] onload-animation" headings={headings} series={series}></SideBar>
```

```ts ins="series={entry.data.series}"
<!-- src\pages\posts[...slug].astro -->
<MainGridLayout banner={entry.data.image} title={entry.data.title} description={entry.data.description} lang={entry.data.lang} setOGTypeArticle={true} headings={headings} series={entry.data.series}>
    // ...
</MainGridLayout>
```

## 创建组件

```ts
<!-- src\utils\content-utils.ts -->
export async function getPostSeries(
  seriesName: string,
): Promise<{ body: string; data: BlogPostData; slug: string }[]> {
  const posts = (await getCollection('posts', ({ data }) => {
    return (
      (import.meta.env.PROD ? data.draft !== true : true) &&
      data.series === seriesName
    )
  })) as unknown as { body: string; data: BlogPostData; slug: string }[]

  posts.sort((a, b) => {
    const dateA = new Date(a.data.published)
    const dateB = new Date(b.data.published)
    return dateA > dateB ? 1 : -1
  })

  return posts
}
```

``` ts
<!-- src\components\widget\Series.astro -->
---
import I18nKey from '../../i18n/i18nKey'
import { i18n } from '../../i18n/translation'
import { getPostSeries } from '../../utils/content-utils'
import { getPostUrlBySlug } from '../../utils/url-utils'
import WidgetLayout from './WidgetLayout.astro'

const COLLAPSED_HEIGHT = '7.5rem'

interface Props {
  class?: string
  style?: string
  series: string
}
const className = Astro.props.class
const style = Astro.props.style
const seriesName = Astro.props.series

const series = await getPostSeries(seriesName)

const isCollapsed = series.length >= 10
---
<WidgetLayout name={i18n(I18nKey.series) + " - " + series[0].data.series} id="series" isCollapsed={isCollapsed} collapsedHeight={COLLAPSED_HEIGHT} class={className} style={style}>
    <div class="flex flex-col gap-1">
        {series.map(t => (
            <a href={getPostUrlBySlug(t.slug)}
                aria-label={t.data.title}
                class="group btn-plain h-10 w-full rounded-lg hover:text-[initial]"
            >
                <!-- dot and line -->
                <div class="w-[15%] md:w-[10%] relative dash-line h-full flex items-center">
                    <div class="transition-all mx-auto w-1 h-1 rounded group-hover:h-5
                    bg-[oklch(0.5_0.05_var(--hue))] group-hover:bg-[var(--primary)]
                    outline outline-4 z-50
                    outline-[var(--card-bg)]
                    group-hover:outline-[var(--btn-plain-bg-hover)]
                    group-active:outline-[var(--btn-plain-bg-active)]
                    "
                    ></div>
                </div>
                <!-- post title -->
                <div class="w-[85%] text-left font-bold
                    group-hover:translate-x-1 transition-all group-hover:text-[var(--primary)]
                    text-75 pr-15 whitespace-nowrap overflow-ellipsis overflow-hidden" title={t.data.title}
                >
                        {t.data.title}
                </div>
            </a>
        ))}
    </div>
</WidgetLayout>
```

## 增加配置项

```ts ins=", \"#series\""
<!-- astro.config.mjs -->
export default defineConfig({
  // ...
  integrations: [
    // ...
	swup({
          theme: false,
          animationClass: "transition-swup-", // see https://swup.js.org/options/#animationselector
          // the default value `transition-` cause transition delay
          // when the Tailwind class `transition-all` is used
          containers: ["main", "#toc", "#series"],
          smoothScrolling: true,
          cache: true,
          preload: true,
          accessibility: true,
          updateHead: true,
          updateBodyClass: false,
          globalInstance: true,
      }),
// ...
```
### 示例

> 这样就配置好了，在文章的模板中增加一条series的参数就可以自动合并同系列文章了

```markdown ins={10}
---
title:
published: 2025-07-31
description: ''
image: ''
tags: []
category: ''
draft: false 
lang: ''
series: '博客改造'
---
```


