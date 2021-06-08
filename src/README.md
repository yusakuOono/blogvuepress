---
title: Home
home: true
heroText: アウトプットするブログ
features:
-
    title: Feature 1
    details: Feature Details
-
    title: Feature 2
    details: Feature Details
-
    title: Feature 3
    details: Feature Details
---
<BlogPostList
  :pages="$site.pages"
  :page-size="$site.themeConfig.pageSize"
  :start-page="$site.themeConfig.startPage"
/>
