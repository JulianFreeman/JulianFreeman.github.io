---
title: 学习 Next.js
date: 2023-11-23 22:02:00 -0500
categories: [翻译, Nextjs]
tags: [nextjs, web]
description: Next.js 官方教程翻译，2023-11-21 版本的，后续若网站内容更新可能会有所不同
media_subpath: /assets/img/learn-nextjs/
---

> 官方教程链接：https://nextjs.org/learn/dashboard-app
{: .prompt-info}

## 学习 Next.js

欢迎来到 Next.js 应用路由教程！在这个免费的交互式课程中，你将会通过构建一个全栈网页应用来学习 Next.js 的主要功能。

> 译注：当然本翻译文件不可能为交互式的啦~
{: .prompt-info}

### 我们要构建什么

![dashboard](dashboard.avif)

在本教程中，我们将会构建一个简化版本的金融仪表盘，并拥有如下功能：

- 一个公共的主页面
- 一个登录页面
- 受身份验证保护的仪表盘页面
- 用户可以添加、编辑和删除单据

这个仪表盘也会附带一个数据库，我们会在[之后的章节](#第六章-设置数据库)中设置。

学完本教程后，你会基本掌握开发全栈 Next.js 应用所需的主要技能。

### 概览

下面是我们要在本教程中学习的功能：

- **样式**：在 Next.js 中设置样式的不同方式。
- **优化**：如何优化图片、链接和字体。
- **路由**：如何使用文件系统路由创建嵌套的布局和页面。
- **数据抓取**：如何在 Vercel 上设置数据库，如何高效抓取和流式传输。
- **搜索和分页**：如何使用 URL 搜索参数实现搜索和分页。
- **更改数据**：如何使用 React 服务器操作更改数据，以及重新验证 Next.js 缓存。
- **错误处理**：如何处理一般的和 `404` 错误。
- **表单验证和访问**：如何做服务器端的表单验证以及提高可访问性的技巧。
- **身份验证**：如何使用 [NextAuth.js](https://next-auth.js.org/) 和中间件为你的应用添加身份验证。
- **元数据**：如何添加元数据以及为你的应用的社交分享做准备。

### 预备知识

本教程假设你对 React 和 JavaScript 有一个基本的了解。如果你不了解 React，我们推荐你先学习 [React 基础]({{site.url}}/posts/react-foundations)，了解组件、属性、状态、钩子，以及一些新功能，比如服务器组件和 Suspense 组件。

### 系统要求

在开始本教程之前，你需要确保你的系统满足以下要求：

- 安装了 Node.js 18.17.0 或更新版本。[在这里下载](https://nodejs.org/en)。
- 操作系统：macOS，Windows（包括 WSL），或者 Linux。

另外，你还需要有一个 [Github 账户](https://github.com/join/)和 [Vercel 账户](https://vercel.com/signup)。

## 第一章 开始准备

### 创建一个新项目

打开终端，[cd](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Understanding_client-side_tools/Command_line#basic_built-in_terminal_commands) 到你想要保存项目的文件夹，然后运行如下命令，来创建一个 Next.js 应用：

```shell
npx create-next-app@latest nextjs-dashboard --use-npm --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example"
```
{: .nolineno}

该命令使用了 [`create-next-app`](https://nextjs.org/docs/app/api-reference/create-next-app)，这是一个命令行界面（CLI）工具，可以帮助你搭建一个 Next.js 应用。在上述命令中，我们也使用了 `--example` 参数，意思是我们将在本教程中使用这个[初学者案例](https://github.com/vercel/next-learn/tree/main/dashboard/starter-example)。

### 浏览项目

与其他要从零写代码的教程不同，本教程的大部分代码我们都为你准备好了。这也跟实际开发时差不多，大部分时候你都是接手一个已经存在的项目并在上面工作。

我们的目标是帮助你专注学习 Next.js 的主要功能，而不是编写一个应用的 *全部* 代码。

安装之后，在代码编辑器中打开项目，然后打开 `nextjs-dashboard`。

```shell
cd nextjs-dashboard
```
{: .nolineno}

让我们花些时间看看这个项目。

#### 文件夹结构

你会发现这个项目有着如下的文件夹结构：

![learn-folder-structure](learn-folder-structure.avif)

- **`/app`**：包含了所有的路由，组件，和应用的逻辑代码，大部分时候你都在这里工作。
- **`/app/lib`**：包含应用使用的函数，比如一些可复用的工具函数或者数据抓取函数。
- **`/app/ui`**：包含了应用的 UI 组件，比如页面卡、表格、表单等。为了节省时间，我们已经为你编写好了这些组件的样式代码。
- **`/public`**：包含了应用的所有静态资产，比如图片。
- **`/scripts`**：包含了一个填充脚本，用来在之后的章节中填充数据库。
- **配置文件**：在应用的根目录下还会有一些配置文件，比如 `next.config.js`{: .filepath}。大部分文件都在你使用 `create-next-app` 创建一个新项目的时候为你创建并配置好了。本教程中你不需要修改这些文件。

你可以尽管浏览文件夹里的所有内容，如果有不懂的，也不用担心。

#### 占位数据

当你构建用户界面的时候，有一些占位数据总是好的。如果还没有数据库或者 API 的话，你可以：

- 使用 JSON 格式的数据或者 JavaScript 对象作占位
- 使用第三方服务比如说 [mockAPI](https://mockapi.io/)。

对于这个项目，我们在 `app/lib/placeholder-data.js`{: .filepath} 中提供了一些占位数据。每一个 JavaScript 对象都表示数据库里的一个表。比如说，对于单据表：

```js
const invoices = [
  {
    customer_id: customers[0].id,
    amount: 15795,
    status: 'pending',
    date: '2022-12-06',
  },
  {
    customer_id: customers[1].id,
    amount: 20348,
    status: 'pending',
    date: '2022-11-14',
  },
  // ...
];
```

在[设置数据库](#第六章-设置数据库)的章节中，我们会使用这些数据来填充数据库（初始化数据库）。

#### TypeScript

你可能也注意到了很多文件都有 `.ts` 或者 `.tsx` 后缀。这是因为这个项目是用 TypeScript 编写的，我们想要编写一个符合现代网站景观的教程。

如果你不懂 TypeScript 也没关系，我们会在需要的时候提供 TypeScript 代码片段。

现在，让我们看一下 `/app/lib/definitions.ts`{: .filepath} 这个文件。这里，我们手动定义了一些会从数据库返回的类型，比如说，单据表会返回如下类型：

```ts
export type Invoice = {
  id: string;
  customer_id: string;
  amount: number;
  date: string;
  // In TypeScript, this is called a string union type.
  // It means that the "status" property can only be one of the two strings: 'pending' or 'paid'.
  status: 'pending' | 'paid';
};
```

使用 TypeScript 可以避免你不小心把错误的数据类型传递给你的组件或者数据库，比如说把一个 `string` 当作 `number` 传递给了 `amount`。

> **如果你是一个 TypeScript 开发者：**
> - 这里我们手动声明了数据类型，但是为了更好的类型安全，我们推荐用 [Prisma](https://www.prisma.io/)，它可以根据你的数据表结构自动生成类型。
> - Next.js 会检测你的项目是否使用了 TypeScript，如果使用了会自动安装必要的包和设置。Next.js 也有一个 [TypeScript 插件](https://nextjs.org/docs/app/building-your-application/configuring/typescript#typescript-plugin)，可以安装在代码编辑器上帮助你自动补全和保证类型安全。

### 运行开发服务器

运行 `npm i` 安装项目的包

```shell
npm i
```
{: .nolineno}

然后运行 `npm run dev` 打开开发服务器

```shell
npm run dev
```
{: .nolineno}

`npm run dev` 会在 `3000` 端口上打开一个 Next.js 开发服务器。让我们来看看这是否管用。在浏览器上打开 http://localhost:3000 ，你的主页应该像下面这样：

![acme-unstyled](acme-unstyled.avif)

## 第二章 CSS 样式

目前为止，你的主页上还没有任何样式。让我们看看下面几种给 Next.js 应用设置样式的不同方式。

> **本章我们会讲到……**
> - 怎么为应用添加全局 CSS 文件。
> - 两种不同的设置样式的方式：Tailwind 和 CSS modules。
> - 如何使用 `clsx` 工具包有选择地添加类名。

### 全局样式

如果你观察 `/app/ui` 文件夹，你会发现里面有一个文件叫 `global.css`。你可以通过在这个文件中添加 CSS 规则来给应用的 **所有** 页面添加样式，比如 CSS 重置规则，HTML 元素（比如链接）的全站样式等。

你可以在应用的任意组件内导入 `global.css`，不过通常来说最好把它加入到最顶级的组件中。在 Next.js 中，这个顶级组件是 [root layout](https://nextjs.org/docs/app/building-your-application/routing/pages-and-layouts#root-layout-required)（稍后讨论）。

找到 `/app/layout.tsx` 文件，然后导入 `global.css` 文件，以为你的应用添加全局样式。

```tsx
import '@/app/ui/global.css';
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

确保开发服务器正常运行，保存修改，然后在浏览器里查看，你的主页现在应该如下图所示：

![home-page-with-tailwind](home-page-with-tailwind.avif)

但是等等，你并没有添加任何 CSS 规则，这些样式是哪来的？

如果你看一看 `global.css` 的内容的话，你会发现有几个 `@tailwind` 声明：

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### Tailwind

[Tailwind](https://tailwindcss.com/) 是一个 CSS 框架，通过允许我们直接在 TSX 标记语言中编写[辅助类](https://tailwindcss.com/docs/utility-first)来帮助我们提高开发进度。

使用 Tailwind 时，你通过添加类名来给元素添加样式，比如说，给 `<h1>` 添加 `"text-blue-500"` 会把文本变蓝：

```html
<h1 className="text-blue-500">I'm blue!</h1>
```

尽管 CSS 样式是全局共享的，但是每个类只单独应用于每个元素。这意味着如果你添加或者删除一个元素，你不用担心去维护额外的样式表、样式冲突，或者担心随着应用越来越大你的 CSS 包也越来越大。

当你使用 `create-next-app` 创建新的项目时，Next.js 会询问你是否想要用 Tailwind。如果你选择 `yes`，Next.js 会自动安装必要的包并为你的应用配置 Tailwind。

如果你看一下 `/app/page.tsx` 文件，你会发现我们这个例子中正在使用 Tailwind 的类。

```tsx
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
 
export default function Page() {
  return (
    // These are Tailwind classes:
    <main className="flex min-h-screen flex-col p-6">
      <div className="flex h-20 shrink-0 items-end rounded-lg bg-blue-500 p-4 md:h-52">
    // ...
  )
}
```

如果这是你第一次使用 Tailwind，不必担心。为了节省时间，我们已经为你把所有要使用的组件都设置好样式了。

让我们玩玩 Tailwind 吧！把下面的代码拷贝并粘贴在 `/app/page.tsx` 文件的 `<p>` 元素的上面。

```html
<div
  className="h-0 w-0 border-b-[30px] border-l-[20px] border-r-[20px] border-b-black border-l-transparent border-r-transparent"
/>
```

如果你更喜欢传统的 CSS 规则，或者你想把你的样式跟 JSX 隔离开，那么 CSS Modules 会是一个不错的选择。

### CSS Modules

[CSS Modules](https://nextjs.org/docs/basic-features/built-in-css-support) 可以在你把 CSS 写入到组件中时自动创建唯一的类名，这样你也不用担心样式冲突。

在本教程我们会使用 Tailwind，但是也让我们来看一看如何使用 CSS Modules 实现上例中相同的效果吧。

在 `/app/ui` 中，创建一个名为 `home.module.css` 的新文件，然后添加如下规则：

```css
.shape {
  height: 0;
  width: 0;
  border-bottom: 30px solid black;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}
```

然后在 `/app/page.tsx` 文件中导入样式，并把你之前添加的 `<div>` 中的 Tailwind 类名替换为 `styles.shape`。

```tsx
import styles from '@/app/ui/home.module.css';
 
//...
<div className="flex flex-col justify-center gap-6 rounded-lg bg-gray-50 px-6 py-10 md:w-2/5 md:px-20">
    <div className={styles.shape}></div>;
// ...
```

保存更改并在浏览器中查看效果。你应该会看到一个跟之前一样的图形。

Tailwind 和 CSS Modules 是给 Next.js 应用添加样式的两大常用方式。用哪一个纯粹是个人喜好了，你甚至也可以都用。

### 使用 `clsx` 库来切换类名

可能有些时候，你需要根据一些状态或者其他情况更改元素的样式。

[`clsx`](https://www.npmjs.com/package/clsx) 是一个可以让你轻松切换类名的库。我们建议查看[官方文档](https://github.com/lukeed/clsx)了解更多细节，下面是一些基本使用：

- 假设你想创建一个名为 `InvoiceStatus` 的组件并接收参数 `status`，这个参数可能是 `"pending"` 或者 `"paid"`。
- 如果是 `"paid"`，颜色就变为绿色，如果是 `"pending"`，颜色就变为灰色。

你可以使用 `clsx` 来根据条件应用不同的类：

```tsx
import clsx from 'clsx';
 
export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

### 其他设置样式的方案

除了我们前面提到的方法，你也可以通过如下方法给 Next.js 应用添加样式：

- Sass，它允许你导入 `.css` 和 `.scss` 文件。
- CSS-in-JS 库，比如 [styled-jsx](https://github.com/vercel/styled-jsx)，[styled-components](https://github.com/vercel/next.js/tree/canary/examples/with-styled-components)，和 [emotion](https://github.com/vercel/next.js/tree/canary/examples/with-emotion)。

查看 [CSS 文档](https://nextjs.org/docs/app/building-your-application/styling)了解更多信息。

## 第三章 优化字体和图片

在之前的章节中，我们学习了如何给 Next.js 应用设置样式。现在让我们继续调整主页，添加一个自定义字体和一张主页图。

> **本章我们会讲到……**
> - 如何使用 `next/font` 添加自定义字体。
> - 如何使用 `next/image` 添加图片。
> - Next.js 是如何优化字体和图片的。

### 为什么要优化字体？

字体在网站设计中占据了相当重要的位置，但如果使用自定义字体时字体文件需要临时抓取和加载，那么就会很影响项目的性能。

[累计布局偏移](https://web.dev/cls/)（CLS）是一项谷歌用来评估一个网站性能和用户体验的指标。当一个网站先用备用字体或者系统字体进行初始化渲染，等自定义字体加载完成后再将字体替换，这样就发生了布局偏移。这种替换会使得字体大小、间隔或者整个布局发生变化，也使得其周围的元素发生偏移。

![font-layout-shift](font-layout-shift.avif)

当你使用 `next/font` 模块的时候，Next.js 会自动为你的应用优化字体。它会在构建时期把字体文件下载下来，然后把它们跟你的其他静态文件放在一起管理。这意味着当有人访问你的网站时，网站不需要额外的网络请求来下载字体，这就提升了网站性能。

### 添加主字体

让我们为你的应用添加一个自定义的谷歌字体吧！

在 `/app/ui` 文件夹，创建一个名为 `fonts.ts` 的新文件。我们将使用这个文件来保存应用中所使用的字体。

从 `next/font/google` 模块导入 `Inter` 字体，这将会是我们的主字体。然后指定要加载哪一个[子集](https://fonts.google.com/knowledge/glossary/subsetting)，在我们的例子中，是 `"latin"`。

```ts
import { Inter } from 'next/font/google';
 
export const inter = Inter({ subsets: ['latin'] });
```

最后，在 `/app/layout.tsx` 文件的 `<body>` 元素中添加该字体。

```tsx
import '@/app/ui/global.css';
import { inter } from '@/app/ui/fonts';
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}
```

给 `<body>` 元素添加 `Inter` 之后，这个字体就会应用到整个应用中。这里，我们也添加了一个 Tailwind 类名 `antialiased` 来使字体更平滑。这不是必需的，但是加上会很不错。

打开浏览器，打开开发者工具，选择 `body` 元素，你应该会看到 `Inter` 和 `Inter_Fallback` 出现在样式中。

### 练习：添加第二字体

你可以给应用中特定的元素添加字体。

现在你自己来操作吧！在 `fonts.ts` 文件中，导入名为 `Lusitana` 的第二字体，然后把它传递给 `/app/page.tsx` 文件的 `<p>` 元素。要像前面那样指定字体子集的话，你还需要指定字体的 **字重**。

当你完成了，可以展开下面的代码片段查看解决方案。

> **提示**：
> - 如果你不知道怎么传递字体的字重选项，看一下代码编辑器报的 TypeScript 错误。
> - 访问[谷歌字体](https://fonts.google.com/)并搜索 `Lusitana` 来查看有哪些子集可选。
> - 查看文档了解如何[添加多个字体](https://nextjs.org/docs/app/building-your-application/optimizing/fonts#using-multiple-fonts)和[所有可用选项](https://nextjs.org/docs/app/api-reference/components/font#font-function-arguments)。

> 解决方案如下（当然无法折叠啦~）

```ts
import { Inter, Lusitana } from 'next/font/google';
 
export const inter = Inter({ subsets: ['latin'] });
 
export const lusitana = Lusitana({
  weight: ['400', '700'],
  subsets: ['latin'],
});
```
{: file="/app/ui/fonts.ts"}

```tsx
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import { lusitana } from '@/app/ui/fonts';
 
export default function Page() {
  return (
    // ...
    <p
      className={`${lusitana.className} text-xl text-gray-800 md:text-3xl md:leading-normal`}
    >
      <strong>Welcome to Acme.</strong> This is the example for the{' '}
      <a href="https://nextjs.org/learn/" className="text-blue-500">
        Next.js Learn Course
      </a>
      , brought to you by Vercel.
    </p>
    // ...
  );
}
```
{: file="/app/page.tsx"}

最后 `<AcmeLogo />` 组件也使用了 Lusitana 字体，之前被注释了，为了防止报错，现在你可以移除它的注释了。

```tsx
// ...
 
export default function Page() {
  return (
    <main className="flex min-h-screen flex-col p-6">
      <div className="flex h-20 shrink-0 items-end rounded-lg bg-blue-500 p-4 md:h-52">
        <AcmeLogo />
        {/* ... */}
      </div>
    </main>
  );
}
```

很好，你现在已经给应用添加了两个自定义字体了，下面，让我们给主页添加一张主页图。

### 为什么要优化图片？

Next.js 在顶层文件夹 `/public` 中管理静态资源，比如图片。`/public` 文件夹内的文件都可以被应用使用。

用常规的 HTML 语法，你需要像这样添加图片：

```html
<img
  src="/hero.png"
  alt="Screenshots of the dashboard project showing desktop version"
/>
```

然而，这也意味着你需要手动做这些：

- 确保你的图片可以在不同的屏幕大小上做出合适的响应
- 为不同的设备指定图片大小
- 防止图片加载时造成的布局偏移
- 在用户视窗之外时延迟加载图片

图片优化是网站开发中的一个大话题，甚至可以自成一派。为了避免手动实现这些优化细节，你可以使用 `next/image` 组件来自动优化图片。

### `<Image>` 组件

`<Image>` 组件是对 HTML `<img>` 标签的扩展，具有自动图片优化的功能，包括：

- 在图片加载时防止自动布局偏移
- 可以自动调整大小，以避免在小窗口的设备上加载大图片
- 默认延迟加载图片（当进入用户视窗时加载图片）
- 如果浏览器支持的话，用现代的图片格式，如 [WebP](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types#webp) 和 [AVIF](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types#avif_image) 管理图片

### 添加桌面端主页图

让我们来使用 `<Image>` 组件吧。如果你打开 `/public` 文件夹，你会看到有两张图片：`hero-desktop.png` 和 `hero-mobile.png`。两张图完全不同，它们一张用于显示在桌面端设备，另一张用于移动端。

在 `/app/page.psx` 文件中，从 [`next/image`](https://nextjs.org/docs/api-reference/next/image) 中导入组件，然后在注释下面添加图片：

```tsx
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import { lusitana } from '@/app/ui/fonts';
import Image from 'next/image';
 
export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Add Hero Images Here */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
    </div>
    //...
  );
}
```

这里，我们把 `width` 设置为 `1000` 像素，把 `height` 设置为 `760` 像素。手动设置图片的宽高来避免布局偏移是一个好办法，这个宽高比需要跟原图片的宽高比 **相同**。

这里我们还添加了 `hidden` 类来从移动端屏幕中移除图片，和 `md:block` 来在桌面端屏幕中显示图片。

![home-page-with-hero](home-page-with-hero.avif)

### 练习：添加移动端的主页图

现在到你了！在刚才添加的图片下面，添加一张 `hero-mobile.png` 的 `<Image>` 组件。

- 图片的 `width` 应该为 `560` 像素，`height` 应该为 `620` 像素。
- 图片应该在移动端屏幕上显示，在桌面端隐藏。你可以通过开发者工具查看图片在桌面端和移动端中是否正常切换。

当你完成之后，可以展开下面的代码查看解决方案。

> 解决方案如下（当然无法折叠啦~）

```tsx
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';
import { lusitana } from '@/app/ui/fonts';
import Image from 'next/image';
 
export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Add Hero Images Here */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
      <Image
        src="/hero-mobile.png"
        width={560}
        height={620}
        className="block md:hidden"
        alt="Screenshot of the dashboard project showing mobile version"
      />
    </div>
    //...
  );
}
```
{: file="/app/page.tsx"}

很好，现在我们的主页有自定义字体和主页图了。

### 推荐阅读

关于这些话题，还有很多内容，比如优化远程图片，或者使用本地的字体文件等。如果想了解更多内容，可以阅读下面的文档：

- [图片优化文档](https://nextjs.org/docs/app/building-your-application/optimizing/images)
- [字体优化文档](https://nextjs.org/docs/app/building-your-application/optimizing/fonts)
- [从图片角度提升网站性能（MDN）](https://developer.mozilla.org/en-US/docs/Learn/Performance/Multimedia)
- [网站字体（MDN）](https://developer.mozilla.org/en-US/docs/Learn/CSS/Styling_text/Web_fonts)

## 第四章 创建布局和页面

目前为止，我们的应用只有一个主页。现在让我们学习如何使用 **布局** 和 **页面** 来创建更多分页。

> **本章我们会讲到……**
> - 使用文件系统路由创建 `dashboard` 分页
> - 理解文件夹和文件在创建新分页时的作用
> - 创建一个嵌套的布局，以便在多个仪表盘分页中共享使用
> - 理解什么是托管、局部渲染和根布局（root layout）

### 嵌套路由

Next.js 使用的是文件系统路由，其中 **文件夹** 被用来嵌套分页。每一个文件夹代表了一个 **段路由**，映射到网址上就是一个 **URL 段**。

![folders-to-url-segments](folders-to-url-segments.avif)

你可以使用 `layout.tsx` 和 `page.tsx` 文件为每一个分页创建不同的 UI。

`page.tsx` 是一种特殊的 Next.js 文件，它导出了一个 React 组件，要想让分页变得可访问就得需要它。在我们的应用中，我们已经有一个这样的文件了：`/app/page.tsx`。它是 `/` 路由段对应的主页面。

要在其下创建分页，我们可以在内创建文件夹，然后在文件夹内创建 `page.tsx` 文件，比如：

![static-site-generation](static-site-generation.avif)

`/app/dashboard/page.tsx` 对应的是 `/dashboard` 网址路径。让我们创建一个页面看看如何。

### 创建一个仪表盘页面

在 `/app` 内创建一个名为 `dashboard` 的文件夹。然后在该文件夹内创建一个新的 `page.tsx` 文件，并包含如下内容：

```tsx
export default function Page() {
  return <p>Dashboard Page</p>;
}
```

现在，确保开发服务器正在运行，然后访问 http://localhost:3000/dashboard ，你应该会看到 “Dashboard Page” 字样。

这就是如何在 Next.js 中创建不同的页面的方式了：创建一个文件夹，代表一个新的段路由，然后添加一个 `page` 文件。

通过这个有特殊名字的 `page` 文件，Next.js 允许我们可以在此路由下[托管](https://nextjs.org/docs/app/building-your-application/routing#colocation) UI 组件、测试文件，和其他相关的代码。只有 `page` 文件中的内容是公开的。比如 `/ui` 和 `/lib` 文件夹是跟其他路由一起 *托管* 在 `/app` 中的。

> 译注：其实我没怎么理解这句话。

### 练习：创建其他仪表盘页面

让我们练习创建更多分页吧。在我们的 `dashboard` 中，创建下面两个页面：

1. **客户分页**：该分页应该通过 http://localhost:3000/dashboard/customers 访问，现在该页面可以只返回一个 `<p>Customers Page</p>` 元素。
2. **单据分页**：该分页应该通过 http://localhost:3000/dashboard/invoices 访问，现在该分页可以只返回一个 `<p>Invoices Page</p>` 元素。

花些时间整整这个练习，当你完成后，展开下面的代码查看解决方案。

> 解决方案如下（当然无法折叠啦~）

你应该有如下文件夹结构：

![routing-solution](routing-solution.avif)

```tsx
export default function Page() {
  return <p>Customers Page</p>;
}
```
{: file="/app/dashboard/customers/page.tsx"}

```tsx
export default function Page() {
  return <p>Invoices Page</p>;
}
```
{: file="/app/dashboard/invoices/page.tsx"}

### 创建仪表盘布局

仪表盘有一些可以在多个页面中共享的导航区域。在 Next.js 中，你可以使用一个特殊的文件 `layout.tsx` 来创建可以在多个页面中共享的 UI。让我们为仪表盘页面创建一个布局吧！

在 `/dashboard` 文件夹内，添加一个名为 `layout.tsx` 的新文件，并粘贴如下代码：

```tsx
import SideNav from '@/app/ui/dashboard/sidenav';
 
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```

这段代码包含了几方面内容，我们一点一点看：

首先，我们在布局中导入了 `<SideNav />` 组件。你在这个文件中导入的任何组件都会成为这个布局的一部分。

`<Layout />` 组件接受一个 `children` 属性，这个子组件可以是一个页面或者是其他的布局。在我们的例子中，在 `/dashboard` 之下的页面会自动嵌套进 `<Layout />` 中，像下面这样：

![shared-layout](shared-layout.avif)

保存更改，检查网页，确保一切运行正常。你应该会看到如下内容：

![shared-layout-page](shared-layout-page.avif)

在 Next.js 中使用布局的其中一个好处就是方便导航，只有页面的组件会重新渲染而布局的不会，这就叫[局部渲染](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#3-partial-rendering)。

![partial-rendering-dashboard](partial-rendering-dashboard.avif)

### 根布局

在第三章，我们在另一个布局文件 `/app/layout.tsx` 中导入了一个 `Inter` 字体，如下

```tsx
import '@/app/ui/global.css';
import { inter } from '@/app/ui/fonts';
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}
```

这个是根布局（[root layout](https://nextjs.org/docs/app/building-your-application/routing/pages-and-layouts#root-layout-required)），是必需的。你在根布局中添加的任何 UI 都会在应用的 **所有** 页面上共享。你可以使用根布局来修改 `<html>` 和 `<body>` 标签，或者添加元数据（我们会在[之后的章节](#第十六章-添加元数据)中学习元数据）。

## 第五章 在页面间导航

在上一章节，我们创建了仪表盘的布局和页面。现在让我们添加一些链接，来允许用户在不同的分页间跳转。

> **本章我们会讲到……**
> - 如何使用 `next/link` 组件
> - 如何使用 `usePathname()` 钩子显示当前激活的链接
> - Next.js 的导航是如何运作的

### 为什么要优化导航？

要在不同的页面间建立链接，你通常会需要 HTML 的 `<a>` 标签。目前为止，侧边栏的链接就是用 `<a>` 链接的，但是观察一下，当你在主页、单据页、客户页之间跳转时，发生了什么？

看到了吗？

每次跳转，整个页面都刷新了！

### `<Link>` 组件

在 Next.js 中，你可以使用 `<Link />` 组件来链接应用中的不同页面。`<Link>` 允许你使用 JavaScript 实现[客户端导航](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#how-routing-and-navigation-works)。

要使用 `<Link />` 组件，打开 `/app/ui/dashboard/nav-links.tsx`，从 `next/link` 导入 `Link` 组件，然后把 `<a>` 标签替换为 `<Link>`。

```tsx
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
 
// ...
 
export default function NavLinks() {
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

正如你所见，`Link` 组件的用法跟 `<a>` 很相似。

保存更改，然后到浏览器查看网页。你现在在页面间导航应该就不会出现一整个页面都刷新的问题了。尽管这个应用的一部分是在服务器端渲染的，但是不会出现整个页面都刷新的问题了，就像一个网页应用一样。为什么会这样？

#### 自动的代码分割和预获取

为了提升导航体验，Next.js 会根据段路由自动进行代码分割。这跟传统的 React [单页应用](https://developer.mozilla.org/en-US/docs/Glossary/SPA)不同，后者是在应用初始化时加载全部的代码。

根据路由分割代码意味着页面之间是独立的。如果某一个页面报错了，剩下的页面依旧能正常工作。

而且，在实际生产环境中，每当 [`<Link>`](https://nextjs.org/docs/api-reference/next/link) 组件出现在浏览器视窗中，Next.js 就会在后台自动 **预获取** 被链接页面的代码。当用户点击链接时，目标页面的代码早就在后台加载好了，这就是为什么页面间的切换好像是瞬间完成的。

了解更多关于[导航](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#how-routing-and-navigation-works)的内容。

### 模式：显示激活链接

一个常见的 UI 模式是向用户展示激活的链接，来告诉用户他们现在在哪个页面上。要实现这一点，你需要从 URL 中获取到用户当前的地址路径。Next.js 提供了一个名叫 `userPathname()` 的钩子，你可以通过它获取地址路径并实现该模式。

因为 [`userPathname()`](https://nextjs.org/docs/app/api-reference/functions/use-pathname) 是一个钩子，你需要把 `nav-links.tsx` 转换为一个客户端组件。在该文件顶端添加一个 React
声明 `use client`，然后从 `next/navigation` 中导入 `usePathname()`：

```tsx
'use client';
 
import {
  UserGroupIcon,
  HomeIcon,
  InboxIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
 
// ...
```

然后在 `<NavLinks />` 组件中把路径赋值给一个叫 `pathname` 的变量：

```tsx
export default function NavLinks() {
  const pathname = usePathname();
  // ...
}
```

你可以使用我们在 [CSS 样式](#第二章-css-样式)那一章讲过的 `clsx` 库来根据链接是否激活这个条件应用不同的类名。当 `link.href` 跟 `pathname` 匹配的时候，链接文本应该变为蓝色，背景应该变为浅蓝。

这是 `nav-links.tsx` 文件的最终代码：

```tsx
'use client';
 
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
import clsx from 'clsx';
 
// ...
 
export default function NavLinks() {
  const pathname = usePathname();
 
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className={clsx(
              'flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3',
              {
                'bg-sky-100 text-blue-600': pathname === link.href,
              },
            )}
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

保存代码，然后查看网页。你应该会看到当前页面的链接是蓝色高亮。

## 第六章 设置数据库

在继续开发仪表盘之前，我们需要一些数据。在这一章，我们将会使用 `@vercel/postgres` 设置一个 PostgreSQL 数据库。如果你已经很熟悉 PostgreSQL 了，并且想自己提供数据，那你可以跳过这一章节，并自己填充数据。如果不是的话，那我们就继续了！

> **本章我们会讲到……**
> - 把项目推到 Github 上
> - 创建一个 Vercel 账号并链接到 Github 仓库，以实现快速预览和部署
> - 创建并把项目链接到一个 PostgreSQL 数据库
> - 给数据库填充数据

### 创建一个 Github 仓库

如果你还没有把项目推到 Github 上，我们现在就来做吧。这会让数据库的设置和部署变得简单些。

如果你需要帮助，可以看一下这个 [Github 教程](https://help.github.com/en/github/getting-started-with-github/create-a-repo)。

> **好消息是**
> - 你也可以使用其他的 Git 服务商，比如 GitLab 或者 Bitbucket。
> - 如果你是 Github 新手，我们推荐使用 [Github 桌面应用](https://desktop.github.com/)来简化开发流程。

### 创建一个 Vercel 账户

访问 https://vercel.com/signup 并创建一个账户。选择免费计划（"hobby"），然后选择 **Continue with Github**，将你的 Github 账户链接到 Vercel。

### 链接并部署你的项目

下一步，你应该会看到如下界面，你可以选择并导入刚才创建的 Github 仓库：

![import-git-repo](import-git-repo.avif)

给你的项目起一个名字然后部署。

![configure-project](configure-project.avif)

乌拉！🎉你的项目已经部署了！

> 译注：如果有任何问题，查看报错信息，你应该可以解决的。

![deployed-project](deployed-project.avif)

链接到 Github 仓库之后，每当你往 **`main`** 分支提交更改时，Vercel 就会自动重新部署你的项目，不需要额外的设置。如果有拉取请求，你也可以有一个[即时预览](https://vercel.com/docs/deployments/preview-deployments#preview-urls)，这样就能尽早地发现部署错误，同时也可以把这个部署的预览发给团队其他成员以获取反馈。

### 创建一个 Postgres 数据库

下一步，设置数据库，点击 **Continue to Dashboard**，然后在项目面板中选择 **Storage**。选择 **Connect Store → Create New → Postgres → Continue**。

![create-database](create-database.avif)

接受条款，给数据库起个名字，确保数据库的区域是 **Washington D.C (iad1)**，这是每一个新 Vercel 项目的[默认区域](https://vercel.com/docs/functions/serverless-functions/regions#select-a-default-serverless-region)。把数据库放在跟应用代码相同或者临近的区域，可以降低数据请求时的[延迟](https://developer.mozilla.org/en-US/docs/Web/Performance/Understanding_latency)。

![database-region](database-region.avif)

> **需要注意的是**，数据库区域一旦设置就不能更改了。如果你想使用不同的[区域](https://vercel.com/docs/storage/vercel-postgres/limits#supported-regions)，就需要在创建数据库之前设置好。

连接好后，切换到 `.env.local` 页面，点击 **Show secret** 和 **Copy Snippet**。确保你复制之前密钥是显示出来的。

![database-dashboard](database-dashboard.avif)

回到代码编辑器，把 `.env.example` 文件重命名为 `.env`，然后把刚才拷贝的内容粘贴到文件中。

**注意**：打开 `.gitignore` 文件，确保里面有 `.env`，这样才不会把你数据库的密钥上传到 Github 上。

最后，在终端运行 `npm i @vercel/postgres`，安装 [Vercel Postgres SDK](https://vercel.com/docs/storage/vercel-postgres/sdk)。

### 填充数据库

现在数据库已经创建好了，让我们填充一些初始数据吧。这会让你在开发仪表盘的时候有一些数据可用。

在项目的 `/scripts` 文件夹中，有一个文件叫 `seed.js`。这个脚本中包含了一些创建并填充表格的指令。

如果你现在看不懂这些代码干了什么，也不用担心，其实这个脚本就是用 **SQL** 创建了一些表，然后把 `placeholder-data.js` 中的数据给填充进去了。

下一步，在 `package.json` 文件中，将下面的行添加进去：

```json
"scripts": {
  "build": "next build",
  "dev": "next dev",
  "start": "next start",
  "seed": "node -r dotenv/config ./scripts/seed.js"
}
```

这是一个会执行 `seed.js` 文件的命令。

现在，运行 `npm run seed`，你应该会在终端中看到一些 `console.log` 的打印信息，这代表脚本正在正常运行。

> **故障排除**：
> - 确保在拷贝数据库密钥的时候点了显示密钥。
> - 脚本使用 `bcrypt` 加密用户的密码，如果 `bcrypt` 跟你的环境不兼容，你也可以修改一下脚本使用 `bcryptjs`。
> - 如果你在填充数据库时遇到问题，想重新运行脚本，你可以在数据库查询界面运行 `DROP TABLE tablename` 来把数据库中已有的表格删除。查看[执行查询区域](#执行查询)了解细节。但是要注意，这条命令会删除数据库中的所有数据表，现在我们搭建的只是一个案例应用，使用的数据都是占位数据，因此没什么关系，但你不应该在实际上线的应用中运行这个命令。
> - 如果你在填充 Vercel Postgres 数据库期间遇到问题，可以在 Github 开一个[讨论区](https://github.com/vercel/next-learn/issues)。

### 浏览数据库

让我们看看现在的数据库里有什么。回到 Vercel，在侧边导航中点击 **Data**。

在这一部分，你会看到有四个新表：users，customers，invoices，和 revenue。

![database-tables](database-tables.avif)

逐个选中表格，检查里面的记录是不是跟 `placeholder-data.js` 里的占位数据一致。

### 执行查询

你可以切换到 Query 选项来与数据库交互。这个区域支持标准的 SQL 命令。比如说，执行 `DROP TABLE customers` 会把 customers 表中的所有数据都删除——这点要小心！

让我们执行第一个数据库查询。把下面的 SQL 代码粘贴到 Vercel 界面中：

```sql
SELECT invoices.amount, customers.name
FROM invoices
JOIN customers ON invoices.customer_id = customers.id
WHERE invoices.amount = 666;
```

然后运行。

## 第七章 抓取数据

现在我们已经有了数据库，也填充了数据，让我们来看看在应用中抓取数据的几种方式吧，同时也开发好仪表盘的预览页面。

> **本章我们会讲到……**
> - 学习几种抓取数据的方式：API，ORM，SQL，等等
> - 服务器组件是如何更安全地帮助我们获取后端资源的
> - 网络瀑布是啥
> - 如何实现用 JavaScript 模式并行抓取数据

### 选择抓取数据的方式

#### API 层

API 是应用和数据库之间的中介层。下面几种情况你可能会需要用 API：

- 如果你使用的是提供 API 的第三方服务。
- 如果你是在客户端抓取数据的，你想在服务器上运行一个 API 层来避免你的数据库密钥暴露给用户

在 Next.js 中，你可以使用 [Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers) 来创建 API 端点。

#### 数据库查询

如果你搭建的是一个全栈应用，你会需要编写一些逻辑代码跟数据库交互。对于像 Postgres 这样的[关系型数据库](https://aws.amazon.com/relational-database/)，你可以用 SQL，或者 [ORM](https://vercel.com/docs/storage/vercel-postgres/using-an-orm#)（比如 [Prisma](https://www.prisma.io/)）来实现这点。

下面几种情况下你可能会需要手动编写数据库查询代码：

- 创建 API 端点，你需要编写跟数据库交互的逻辑代码。
- 如果你使用的是 React 服务器组件（在服务器上抓取数据），你可以跳过 API 层，直接向数据库查询数据，而不用担心数据库密钥暴露给用户。

让我们了解一些关于 React 服务器组件的内容。

#### 使用服务器组件抓取数据

默认情况下，Next.js 应用使用的都是 **React 服务器组件**。用服务器组件抓取数据是一种相对较新的方式，有以下几种好处：

- 服务器组件支持 promise，这是一种解决像抓取数据这样的异步任务的简单方案。你可以使用 `async/await` 这样的语法，而不用涉及 `useEffect`，`useState` 或者其他的抓取数据的库。
- 服务器组件在服务器上运行，因此你可以把重要数据的抓取和逻辑保留在服务器端，只把结果发送给用户。
- 前面提到了，因为服务器组件是在服务器上运行的，你可以直接查询数据库而不用额外罩一层 API 了。

#### 使用 SQL

对于我们的仪表盘项目，我们会使用 [Vercel Postgres SDK](https://vercel.com/docs/storage/vercel-postgres/sdk) 和 SQL 来编写数据库查询。下面是我们这么做的几点原因：

- SQL 是查询关系型数据库的行业标准（比如，ORM 在底层也是生成了 SQL）。
- 对于 SQL 有一个基本的了解可以帮助我们理解关系型数据库的基础，有助于我们使用其他工具。
- SQL 很强大，可以允许我们抓取和操作特定的数据。
- Vercel Postgres SDK 可以防止 [SQL 注入](https://vercel.com/docs/storage/vercel-postgres/sdk#preventing-sql-injections)。

如果你之前没用过 SQL 也不用担心，我们已经给你写好了查询语句。

打开 `/app/lib/data.ts`，你会看到我们从 `@vercel/postgres` 中导入了 [`sql`](https://vercel.com/docs/storage/vercel-postgres/sdk#sql)，我们可以用这个函数查询数据库。

```ts
import { sql } from '@vercel/postgres';
 
// ...
```

你可以在任意服务器组件中调用 `sql`。但是为了让你在组件间导航更容易些，我们把所有的数据查询都放在了 `data.ts` 文件中，然后可以在其他组件中导入它们。

> **注意**：如果你在第六章时使用了自己的数据库，那么你需要修改 `/app/lib/data.ts` 的查询语句来适配你的数据库。

### 为仪表盘预览页面抓取数据

现在理解了抓取数据的各种不同方式，让我们抓点数据吧。打开 `/app/dashboard/page.tsx`，粘贴下面的代码，然后花点时间过一遍：

```tsx
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
 
export default async function Page() {
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        {/* <Card title="Collected" value={totalPaidInvoices} type="collected" /> */}
        {/* <Card title="Pending" value={totalPendingInvoices} type="pending" /> */}
        {/* <Card title="Total Invoices" value={numberOfInvoices} type="invoices" /> */}
        {/* <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        /> */}
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        {/* <RevenueChart revenue={revenue}  /> */}
        {/* <LatestInvoices latestInvoices={latestInvoices} /> */}
      </div>
    </main>
  );
}
```

在上面的代码中：

- `Page` 是一个 **异步** 组件，它可以允许你使用 `await` 来抓取数据。
- 有三个组件可以接受数据，它们分别是 `<Card>`，`<RevenueChart>`，和 `<LatestInvoices>`。目前先将它们注释了，避免报错。

### 为 `<RevenueChart>` 抓取数据

要为 `<RevenueChart />` 组件抓取数据，从 `data.ts` 中导入 `fetchRevenue` 函数并在组件内调用：

```tsx
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue } from '@/app/lib/data';
 
export default async function Page() {
  const revenue = await fetchRevenue();
  // ...
}
```

然后，移除 `<RevenueChart />` 组件的注释，打开 `/app/ui/dashboard/revenue-chart.tsx` 文件并把里面的代码解注释。查看本地网页，你应该会看到一个使用了 `revenue` 数据的表格。

![recent-revenue](recent-revenue.avif)

让我们继续获取更多数据吧！

### 为 `<LatestInvoices>` 抓取数据

对于 `<LatestInvoices />` 组件，我们需要获取最新的 5 条单据，按照日期排序。

你可以抓取所有的单据并用 JavaScript 排序。对于少量数据来说这没什么问题，但如果你的应用逐渐变大，每次请求获取的数据和 JavaScript 要处理的数据就会显著地增长。

我们不抓取所有数据然后再排序，相反，我们编写 SQL 查询语句只抓取 5 条单据。这个例子中，下面是我们提到的查询语句，它在 `data.ts` 文件中：

```ts
// Fetch the last 5 invoices, sorted by date
const data = await sql<LatestInvoiceRaw>`
  SELECT invoices.amount, customers.name, customers.image_url, customers.email
  FROM invoices
  JOIN customers ON invoices.customer_id = customers.id
  ORDER BY invoices.date DESC
  LIMIT 5`;
```

在 `/app/dashboard/page.tsx` 中，导入 `fetchLatestInvoices` 函数：

```tsx
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue, fetchLatestInvoices } from '@/app/lib/data';
 
export default async function Page() {
  const revenue = await fetchRevenue();
  const latestInvoices = await fetchLatestInvoices();
  // ...
}
```

然后，把 `<LatestInvoices />` 组件解注释，你还需要在 `/app/ui/dashboard/latest-invoices.tsx` 文件中把跟 `<LatestInvoices />` 组件有关的代码也解注释。

此时查看本地网页，你应该会看到数据库上最新的 5 条单据显示出来了。希望你现在能开始感受到直接查询数据库的优势了！

![latest-invoices](latest-invoices.avif)

### 练习：为 `<Card>` 组件抓取数据

现在，到你来为 `<Card>` 组件抓取数据了。卡片会展示如下数据：

- 单据的总金额
- 待办单据的总金额
- 单据的总数量
- 用户的总数量

这里，你可能还是会想先抓取所有的数据，然后用 JavaScript 取处理。比如说用 `Array.length` 来获取单据和用户的总数。

```js
const totalInvoices = allInvoices.length;
const totalCustomers = allCustomers.length;
```

但是有了 SQL，你可以只抓取你需要的数据。下面的代码比上面的稍微长了那么一点，但是它转移的数据比上面的要少很多：

```js
const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
```

这次你要导入的函数叫 `fetchCardDate`，你需要把这个函数返回的值解构。

> **提示**：
> - 查看卡片组件都需要哪些数据。
- 查看 `data.ts` 文件，看看函数返回了什么。

当你完成后，你可以展开如下代码查看最终的代码：

> 解决方案如下（当然无法折叠啦~）

```tsx
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import {
  fetchRevenue,
  fetchLatestInvoices,
  fetchCardData,
} from '@/app/lib/data';
 
export default async function Page() {
  const revenue = await fetchRevenue();
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Card title="Collected" value={totalPaidInvoices} type="collected" />
        <Card title="Pending" value={totalPendingInvoices} type="pending" />
        <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
        <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        />
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <RevenueChart revenue={revenue} />
        <LatestInvoices latestInvoices={latestInvoices} />
      </div>
    </main>
  );
}
```
{: file="/app/dashboard/page.tsx"}

很好！你现在已经把所有预览页需要的数据都抓取到了，你的页面应该如下图所示：

![complete-dashboard](complete-dashboard.avif)

然而，这里有两件事情你需要注意：

1. 数据请求无意中相互阻塞了，造成了 **请求瀑布**。
2. 默认情况下，Next.js 会 **预渲染** 页面以提高性能，这被叫做 **静态渲染**。如果你的数据更改了，仪表盘上不会有相应的更改。

### 什么是请求瀑布？

“瀑布”指的是有一系列的网络请求，每一个请求都只能在之前的请求结束后才能开始。在本例中，只有上一个请求返回数据了，下一个请求才开始。

![sequential-parallel-data-fetching](sequential-parallel-data-fetching.avif)

比如说，我们需要等 `fetchRevenue()` 结束了才能开始执行 `fetchLatestInvoices()`，以此类推。

```tsx
const revenue = await fetchRevenue();
const latestInvoices = await fetchLatestInvoices(); // wait for fetchRevenue() to finish
const {
  numberOfInvoices,
  numberOfCustomers,
  totalPaidInvoices,
  totalPendingInvoices,
} = await fetchCardData(); // wait for fetchLatestInvoices() to finish
```

这种模式倒不是说不对。有些情况下你的确需要这些瀑布，你希望等一个请求完全结束后再进行下一个请求，例如你可能需要先抓取用户的 ID 和简介，再抓取他们的好友列表。这种情况下，后一个请求依赖于前一个请求返回的数据。

然而，这种现象有时候也可能是无意的，并且会影响性能。

### 并行抓取数据

一个避免请求瀑布的常见方法就是同一时间初始化所有数据请求，也就是并行地请求。

在 JavaScript 中，你可以使用 [`Promise.all()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 或者 [`Promise.allSettled()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled) 函数来同时初始化所有的 promises。比如说，在 `data.ts` 中，我们可以在 `fetchCardData()` 函数中使用 `Promise.all()`：

```ts
export async function fetchCardData() {
  try {
    const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
    const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
    const invoiceStatusPromise = sql`SELECT
         SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
         SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
         FROM invoices`;
 
    const data = await Promise.all([
      invoiceCountPromise,
      customerCountPromise,
      invoiceStatusPromise,
    ]);
    // ...
  }
}
```

通过这种模式，你可以：

- 同时执行所有的数据抓取，可以提升性能。
- 使用 JavaScript 原生的模式，可以应用到任意第三方库或者框架。

但是，使用这种 JavaScript 模式有一个 **问题**：如果其中一个数据请求比其他几个慢怎么办？

## 第八章 静态和动态渲染

在上一章节，我们为仪表盘的预览页面抓取了数据。然而，我们也简单讨论了一下产生的两个局限：

1. 这些数据请求无意中造成了网络瀑布。
2. 仪表盘是静态的，因此有数据更新时不会呈现在应用上。

> **本章我们会讲到……**
> - 静态渲染是什么，为什么它能提升应用的性能
> - 动态渲染是什么，什么时候用
> - 让仪表盘变成动态的几种方法
> - 模拟一个很慢的数据请求来看看会发生什么

### 什么是静态渲染？

静态渲染，是指数据抓取和渲染发生在服务器部署阶段，或者重新验证阶段。请求和渲染的结果可以缓存在[内容分发网络（CDN）](https://nextjs.org/docs/app/building-your-application/rendering/server-components#static-rendering-default)并稍后分发。

![static-site-generation-2](static-site-generation.avif)

当用户访问你的网站时，缓存的数据就会被发送。静态渲染有以下几个好处：

- **更快的网站**：预渲染的内容可以被缓存并且大范围发放。这确保世界各地的用户可以更快更稳定地访问网站的内容。
- **降低服务器负载**：因为内容已经缓存了，服务器不需要为每一个用户请求动态生成数据。
- **SEO**：预渲染的内容更便于搜索引擎爬虫建立索引。因为在页面加载时内容就已经存在了。这可以提高内容在搜索引擎中的排名。

静态渲染对于**不需要数据**的 UI 或者 **在所有用户间共享数据** 的 UI 是很有用的，比如一篇静态的博客，或者一个产品页。但对于一个经常更新用户数据的仪表盘来说，这就不太合适了。

与静态渲染相对的就是动态渲染。

### 什么是动态渲染？

动态渲染指的是内容在每次用户 **发送请求**（访问网页）的时候，由服务器重新渲染。动态渲染有以下几个好处：

- **实时数据**：动态渲染允许应用展示实时或者经常更新的数据。
- **特定用户的内容**：展示各个用户自己的数据就很容易了，比如仪表盘或者用户简介，然后根据用户行为更新数据。
- **请求时信息**：动态渲染允许你获取只有在请求时才能知道的信息，比如 cookies，或者 URL 搜索参数。

### 把仪表盘变成动态的

默认情况下，`@vercel/postgres` 自己没有缓存功能。这就是说框架需要设定它自己的静态或者动态行为。

你可以在服务器组件或者数据抓取函数中使用一个叫 `unstable_noStore` 的 Next.js API 来取消静态渲染。我们来试试：

在 `data.ts` 文件中，从 `next/cache` 中导入 `unstable_noStore`，然后在数据抓取函数中先调用它：

```ts
// ...
import { unstable_noStore as noStore } from 'next/cache';
 
export async function fetchRevenue() {
  // Add noStore() here to prevent the response from being cached.
  // This is equivalent to in fetch(..., {cache: 'no-store'}).
  noStore();
 
  // ...
}
 
export async function fetchLatestInvoices() {
  noStore();
  // ...
}
 
export async function fetchCardData() {
  noStore();
  // ...
}
 
export async function fetchFilteredInvoices(
  query: string,
  currentPage: number,
) {
  noStore();
  // ...
}
 
export async function fetchInvoicesPages(query: string) {
  noStore();
  // ...
}
 
export async function fetchFilteredCustomers(query: string) {
  noStore();
  // ...
}
 
export async function fetchInvoiceById(query: string) {
  noStore();
  // ...
}
```

> **注意**：`unstable_noStore` 是一个试验 API，可能将来会改变，如果你想在项目中用一个更稳定的 API，可以试试[路由段配置选项](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config) `export const dynamic = "force-dynamic"`。

### 模拟一个很慢的数据抓取

把仪表盘变为动态是一个很好的开始。然后，我们上一章节提到的问题依然存在。如果一个数据请求比其他的慢怎么办？

让我们来模拟一个很慢的数据请求。在 `data.ts` 文件中，把 `fetchRevenue()` 中的 `console.log` 和 `setTimeout` 解注释。

```ts
export async function fetchRevenue() {
  try {
    // We artificially delay a response for demo purposes.
    // Don't do this in production :)
    console.log('Fetching revenue data...');
    await new Promise((resolve) => setTimeout(resolve, 3000));
 
    const data = await sql<Revenue>`SELECT * FROM revenue`;
 
    console.log('Data fetch completed after 3 seconds.');
 
    return data.rows;
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Failed to fetch revenue data.');
  }
}
```

现在在一个新标签页访问 http://localhost:3000/dashboard/ ，你应该会看到页面花了很长时间加载。在终端中，你应该会看到如下信息：

```text
Fetching revenue data...
Data fetch completed after 3 seconds.
```

这里我们添加了一个 3 秒的延迟来模拟一个很慢的数据请求。结果是在数据抓取的时候你整个页面都卡住了。

这就是开发者要解决的一个常见问题：

使用动态渲染的时候，**你的应用的响应速度是跟你那个最慢的数据请求速度一样的**。

## 第九章 流

在上一章节，我们把仪表盘改成动态渲染了，我们也讨论了很慢的数据抓取是如何影响网页的性能的。让我们看看在数据请求很慢的情况下怎么优化用户体验。

> **本章我们会讲到……**
> - 流是什么，什么时候用
> - 怎么用 `loading.tsx` 和 Suspense 实现流
> - 加载骨架是什么
> - 路由组是什么，什么时候用
> - 要把网页的哪些部分用 Suspense 包起来

### 什么是流？

流是一种数据传输技术，它可以把一个页面分成几个小“块”，然后从服务端一点点把这些块发送到（流到）用户端。

![server-rendering-with-streaming](server-rendering-with-streaming.avif)

有了流，我们就可以防止一些很慢的数据抓取阻塞住我们的整个页面。这就可以让用户先看到页面的一部分，而不用等着所有的数据都加载完才能看到页面。

![server-rendering-with-streaming-chart](server-rendering-with-streaming-chart.avif)

React 的组件模型可以很好地跟流技术适配，因为每一个组件都可以被视为一个 *块*。

在 Next.js 中实现流有两种方式：

1. 在页面层面使用 `loading.tsx` 文件。
2. 对于特定的组件，使用 `<Suspense>`。

让我们看看该怎么做。

### 用 `loading.tsx` 流式加载整个页面

在 `/app/dashboard` 文件夹中，添加一个名为 `loading.tsx` 的新文件：

```tsx
export default function Loading() {
  return <div>Loading...</div>;
}
```

刷新 http://localhost:3000/dashboard ，你应该会看到：

![loading-page](loading-page.avif)

这里发生了如下几件事：

1. `loading.tsx` 是一个建立在 Suspense 之上的特殊的 Next.js 文件。它允许你在数据还在加载时创建一个备用 UI 界面展示给用户。
2. 因为 `<Sidebar>` 是静态的，所以它立刻就显示出来了。在数据还在加载时，用户依然可以点击侧边栏。
3. 用户不需要等待页面全部加载完毕之后才能跳转走（这叫可中断跳转）。

恭喜你，你刚刚实现了流！但是我们需要再做些什么来提升用户体验。让我们把 `Loading...` 这段文本替换为加载骨架。

#### 添加加载骨架

加载骨架是一个简化版本的 UI 界面。很多网站都用它来作为占位 UI（备用 UI）来提示用户内容还在加载中。你在 `loading.tsx` 中嵌入的任何 UI 都会成为静态文件的一部分，并且先被发送。然后，其他的动态数据会一点点从服务端流向用户端。

在 `loading.tsx` 文件中，导入一个叫 `<DashboardSkeleton>` 的新组件：

```tsx
import DashboardSkeleton from '@/app/ui/skeletons';
 
export default function Loading() {
  return <DashboardSkeleton />;
}
```

然后刷新 http://localhost:3000/dashboard 你应该会看到如下界面：

![loading-page-with-skeleton](loading-page-with-skeleton.avif)

#### 解决路由组加载骨架的 bug

现在，这个加载骨架也会应用到单据页面和用户页面。因为 `loading.tsx` 是一个比 `/invoices/page.tsx` 和 `/customers/page.tsx` 更高层的文件。

我们可以用[路由组](https://nextjs.org/docs/app/building-your-application/routing/route-groups)修正这一点。在 `dashboard` 文件夹中创建一个名为 `/(overview)` 的文件夹，然后把 `loading.tsx` 和 `page.tsx` 放进去：

![route-group](route-group.avif)

现在 `loading.tsx` 就只会出现在仪表盘的预览页面了。

路由组允许你把一些文件组织进一些逻辑上的组，而不会影响 URL 路径结构。当你用圆括号 `()` 创建文件夹时，这个文件夹的名字不会出现在 URL 路径中，所以 `/dashboard/(overview)/page.tsx` 实际就是 `/dashboard`。

这里我们用路由组来确保 `loading.tsx` 只应用到仪表盘预览页面。然而，你也可以使用路由组把应用分成不同的部分，比如 `(marketing)` 和 `(shop)` 等。

#### 流式传输一个组件

目前，我们是在整个页面上应用流式传输的。但是，实际上我们可以有细粒度更高地控制，使用 React Suspense 流式传输特定的组件。

Suspense 允许你可以推迟渲染部分应用页面，直到某些条件符合时才渲染（比如数据加载之后）。你可以把要动态渲染的组件用 Suspense 包起来。然后，把一个备用组件传递过去，作为动态组件加载时展示给用户的内容。

如果你还记得 `fetchRevenue()`，它是那个把整个页面都拖垮的慢速数据请求。为了不阻塞整个页面，我们可以只把这个组件用 Suspense 设置为流式传输，而其他的页面 UI 都是立即呈现。

要实现这点，我们需要把数据抓取函数放到组件中。让我们来更新一下代码：

在 `/dashboard/(overview)/page.tsx` 中，把 `fetchRevenue()` 的所有实例都删除

```tsx
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices, fetchCardData } from '@/app/lib/data'; // remove fetchRevenue
 
export default async function Page() {
  const revenue = await fetchRevenue // delete this line
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    // ...
  );
}
```

然后，从 React 导入 `<Suspense>`，用它把 `<RevenueChart />` 包起来。你可以把备用组件 `<RevenueChartSkeleton>` 传递过去。

```tsx
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices, fetchCardData } from '@/app/lib/data';
import { Suspense } from 'react';
import { RevenueChartSkeleton } from '@/app/ui/skeletons';
 
export default async function Page() {
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Card title="Collected" value={totalPaidInvoices} type="collected" />
        <Card title="Pending" value={totalPendingInvoices} type="pending" />
        <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
        <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        />
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <Suspense fallback={<RevenueChartSkeleton />}>
          <RevenueChart />
        </Suspense>
        <LatestInvoices latestInvoices={latestInvoices} />
      </div>
    </main>
  );
}
```

最后，在 `/app/ui/dashboard/revenue-chart.tsx` 文件中更新 `<RevenueChart>` 组件，让它自己抓取数据，而不用参数传递了：

```tsx
import { generateYAxis } from '@/app/lib/utils';
import { CalendarIcon } from '@heroicons/react/24/outline';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue } from '@/app/lib/data';
 
// ...
 
export default async function RevenueChart() { // Make component async, remove the props
  const revenue = await fetchRevenue(); // Fetch data inside the component
 
  const chartHeight = 350;
  const { yAxisLabels, topLabel } = generateYAxis(revenue);
 
  if (!revenue || revenue.length === 0) {
    return <p className="mt-4 text-gray-400">No data available.</p>;
  }
 
  return (
    // ...
  );
}
```

现在，刷新页面，你应该会看到仪表盘的其他信息都立刻显示了，只有 `<RevenueChart>` 展示了一个备用组件骨架。

![loading-revenue-chart](loading-revenue-chart.avif)

#### 练习：流式传输 `<LatestInvoices>`

现在到你了！练习一下刚才学到的内容，把 `<LatestInvoices>` 组件改为流式传输。

把 `fetchLatestInvoices()` 移动到 `<LatestInvoices>` 组件中，用 `<Suspense>` 把组件包起来，然后传递一个叫 `<LatestInvoicesSkeleton>` 的备用组件。

当你做完后，展开下面的代码查看解决方案：

> 解决方案如下（当然无法折叠啦~）

```tsx
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchCardData } from '@/app/lib/data'; // Remove fetchLatestInvoices
import { Suspense } from 'react';
import {
  RevenueChartSkeleton,
  LatestInvoicesSkeleton,
} from '@/app/ui/skeletons';
 
export default async function Page() {
  // Remove `const latestInvoices = await fetchLatestInvoices()`
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Card title="Collected" value={totalPaidInvoices} type="collected" />
        <Card title="Pending" value={totalPendingInvoices} type="pending" />
        <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
        <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        />
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <Suspense fallback={<RevenueChartSkeleton />}>
          <RevenueChart />
        </Suspense>
        <Suspense fallback={<LatestInvoicesSkeleton />}>
          <LatestInvoices />
        </Suspense>
      </div>
    </main>
  );
}
```
{: file="/app/dashboard/(overview)/page.tsx"}

`<LatestInvoices>` 组件记得删除参数！

```tsx
import { ArrowPathIcon } from '@heroicons/react/24/outline';
import clsx from 'clsx';
import Image from 'next/image';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices } from '@/app/lib/data';
 
export default async function LatestInvoices() { // Remove props
  const latestInvoices = await fetchLatestInvoices();
 
  return (
    // ...
  );
}
```
{: file="/app/ui/dashboard/latest-invoices.tsx"}

### 组件编组

很好，你已经坚持到这儿了！现在你需要把 `<Card>` 组件也用 Suspense 包起来。你可以为每一个卡片单独抓取数据，但是这样会让卡片有一种一个个 *弹出* 的效果，可能会让用户看上去不舒服。

所以，你打算怎么解决这个问题呢？

要创建一种更 *交错* 的效果，我们可以用一个包装组件把卡片包起来。这就是说，静态的 `<Sidebar />` 先出现，然后卡片出现：

在 `/app/dashboard/page.tsx` 文件中，

1. 删除 `<Card>` 组件。
2. 删除 `fetchCardData()` 函数。
3. 导入一个新的 **包装** 组件 `<CardWrapper />`。
4. 到一个新的 **骨架** 组件 `<CardSkeleton />`。
5. 用 Suspense 把 `<CardWrapper />` 包起来。

```tsx
import CardWrapper from '@/app/ui/dashboard/cards';
// ...
import {
  RevenueChartSkeleton,
  LatestInvoicesSkeleton,
  CardsSkeleton,
} from '@/app/ui/skeletons';
 
export default async function Page() {
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Suspense fallback={<CardsSkeleton />}>
          <CardWrapper />
        </Suspense>
      </div>
      // ...
    </main>
  );
}
```

然后打开 `/app/ui/dashboard/cards.tsx`，导入 `fetchCardData()` 函数，在 `<CardWrapper />` 组件中调用。确保你把这个组件中的必需代码都解注释了。

```tsx
// ...
import { fetchCardData } from '@/app/lib/data';
 
// ...
 
export default async function CardWrapper() {
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    <>
      <Card title="Collected" value={totalPaidInvoices} type="collected" />
      <Card title="Pending" value={totalPendingInvoices} type="pending" />
      <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
      <Card
        title="Total Customers"
        value={numberOfCustomers}
        type="customers"
      />
    </>
  );
}
```

刷新页面，你应该会看到所有的卡片都在同时加载进来。当你想让多个组件同时加载的时候，可以使用这种模式。

### 决定在哪里放置 Suspense

在哪里放置 Suspense 取决于几件事：

1. 页面流式传输时，你想让用户看到什么。
2. 哪些内容要优先。
3. 组件是否依赖于数据抓取。

看一看你的仪表盘页面，有哪些地方你想改一改的吗？

不用担心，这个没有标准答案。

- 你可以把 **整个页面** 都用流式传输，就像我们用 `loading.tsx` 那样，不过如果某个数据抓取很慢，那就会让整个页面的加载时间变长。
- 你可以对 **每个组件** 单独用流式传输，但这会让 UI 组件 *弹射* 到屏幕上。
- 你可以通过流式传输不同的 **页面区域** 来创建 *交错* 效果，但是这需要你创建包装组件。

在哪里放置 Suspense 会根据应用的不同而不同。通常来说，我们会把数据抓取放到需要它的组件中，然后把组件用 Suspense 包起来。但是如果你想按照页面区域流式传输，或者直接流式传输整个页面，这也没什么错，只要你的应用需要。

大胆去使用 Suspense 吧，看看哪个方案效果更好。这是个十分强大的 API，可以帮助我们创建更愉悦的用户体验。

### 前瞻

流式传输和服务器组件为我们的数据抓取和加载状态提供了新的方式，并最终以提升用户体验为目标。

在下一章，我们会学习局部预渲染，一种依托于流式传输建立的新的 Next.js 渲染模型。

## 第十章 局部预渲染（可选）

> 局部预渲染是一个 Next.js 14 引入的试验功能。随着这项功能逐渐趋于稳定，本页面的内容可能会发生变化。如果你不想使用试验功能，可以跳过这一章节。该章节不是学习本教程的必需章节。

> **本章我们会讲到……**
> - 局部预渲染是什么
> - 它是怎么工作的

### 将静态和动态内容结合起来

现在，如果你在页面中调用一个[动态函数](https://nextjs.org/docs/app/building-your-application/routing/route-handlers#dynamic-functions)（比如 `noStore()` 或者 `cookies()` 等），你的整个页面都会变成动态的。

这其实跟现在大多数网站的构建方式一致，在你为 **整个网站** 或者 **特定页面** 选择渲染方式时，你只能选择要么是静态的，要么是动态的。

但是，大多数页面并不是全静态或者全动态。你的页面可能既有静态内容也有动态内容。比如说，你有一个社交媒体平台，上面的贴子是静态的，但贴子的点赞数是动态的。或者一个电子商务网站，上面的产品细节是静态的，但用户的购物车是动态的。

回到我们的仪表盘页面，哪些组件你认为是静态的，哪些又是动态的？

当你想好后，可以展开下面的内容，看看我们怎么划分仪表盘页面。

> 解决方案如下（当然无法折叠啦~）

![dashboard-static-dynamic-components](dashboard-static-dynamic-components.avif)

- `<SideNav>` 组件既不依赖于数据，也不是特定于某个用户的，因此是 **静态** 的。
- `<Page>` 中的组件依赖于数据，会经常变化，而且有特定于用户的内容，因此是 **动态** 的。

### 局部预渲染是什么？

在 Next.js 14 中，有一个新的预览版渲染模型，叫 **局部预渲染**。它是一个试验功能，允许你在渲染页面时使用一个静态的加载外壳，同时保持某些部分是动态的。换句话说，你可以把页面的动态部分隔离出来。

![thinking-in-ppr](thinking-in-ppr.avif)

当用户访问页面时：

- 一个静态的页面 *外壳* 先渲染，这会加快初次加载的速度。
- 这个外壳会留下一些 *洞*，给那些动态内容异步加载。
- 这些异步的内容是并行加载的，减少了整个页面的加载时间。

这跟咱们编写的应用不同，咱们的页面要么是全静态的，要么是全动态的。

局部预渲染把极快的静态内容分发和全部的动态功能结合在了一起。我们相信它有[成为网页应用默认渲染模型](https://vercel.com/blog/partial-prerendering-with-next-js-creating-a-new-default-rendering-model)的潜力，同时带来最好的静态网站生成和动态内容分发。

### 局部预渲染是怎么工作的？

局部预渲染利用了 React 的并发 API，使用 Suspense 来延迟部分页面的渲染，直到它们符合某种条件（比如数据加载完了）。

备用 UI 是跟其他静态内容一起待在初始化的静态文件中。在构建阶段（或者重新验证阶段），页面的静态内容会 *预渲染*，然后剩下的会延后，直到用户访问这些页面才渲染。

值得一提的是，把一个组件包进 Suspense 中并不会把这个组件变成动态的（还记得你是通过 `unstable_noStore` 来实现这点的），Suspense 只是页面中静态和动态内容的一个分界线。

关于局部预渲染很好的一方面是，使用它时你不需要额外修改代码。只要你是用 Suspense 在页面中包裹动态内容的，Next.js 就会知道你页面的哪部分内容是静态的，哪部分是动态的。

> **提示**：要学习更多关于如何配置局部预渲染，你可以查看[局部预渲染文档](https://nextjs.org/docs/app/api-reference/next-config-js/partial-prerendering)，或者尝试[局部预渲染模板和演示](https://vercel.com/templates/next.js/partial-prerendering-nextjs)。请牢记，这个功能是 **试验性** 的，还 **不能用于生产环境部署中**。

### 总结

回顾一下，在我们的应用中，我们做了如下几件事来优化数据抓取：

1. 把数据库创建在跟应用代码同一区域，以降低服务器和数据库之间的延迟。
2. 用 React 服务器组件在服务器上抓取数据。这可以让你把重要的数据抓取和逻辑保留在服务器端，减少用户端的 JavaScript 捆绑包，同时防止数据库密钥暴露给用户。
3. 使用 SQL 只抓取你需要的数据，减少每次请求时的数据传输量，也减少 JavaScript 需要在内存中处理的数据量。
4. 用 JavaScript 并行抓取数据——在需要时这么做。
5. 实现流式传输以防止慢速的数据抓取阻塞住整个页面，也让用户可以在数据加载完之前就能与界面 UI 交互。
6. 把数据抓取函数移动到需要它们的组件中，这样可以把页面中需要动态内容的部分隔离开，以便使用局部预渲染。

下一章，我们将会学习两种你在抓取数据时可能会需要的模式：搜索和分页。

## 第十一章 添加搜索和分页 

## 第十六章 添加元数据
