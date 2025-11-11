---
title: 解决Ionic无法监听滚动(scroll)事件
published: "2023-08-02 19:55:31"
draft: false
description: 如果你尝试直接在window元素监听scroll事件，事件是无法触发的，只有wheel事件能够被触发。
author: 路上看见
tags:
  - 默认分类
coverImage:
  src: ./4101364345.webp
  alt: 解决Ionic无法监听滚动(scroll)事件
toc: true
---


如果你尝试直接在window元素监听scroll事件，事件是无法触发的，只有wheel事件能够被触发。

只有监听ion-content元素下shadow-root中的main元素的scroll才能正确的被触发。

你可以通过调用ion-content中的方法`getScrollElement`获取到滚动元素。

示例代码 (Vue3):

    # Ionic-content元素
    const contentRef: Ref<HTMLIonContentElement | null> = ref(null)
    
    onMounted(() => {
      contentRef.getScrollElement().then((e) => {
        e.addEventListener('scroll', (e) => console.log('scroll event'));
      });
    });

或者你也可以使用ionic提供的ionScroll事件来监听滚动（需要先启用scrollEvents，但在我的测试下此事件无法使用），详细请见[https://ionicframework.com/docs/api/content#scrollevents][1]

这是为什么呢:

> 这是因为ionic-content组件提供了一个易于使用的内容区域，并提供了一些有用的方法来控制可滚动区域。在单个视图中应该只有一个内容。Content可以与许多其他Ionic组件一起自定义，以修改其填充、边距等，使用CSS
> Utilities提供的全局样式或使用CSS和可用的CSS自定义属性单独对其进行样式设置。Content提供了一些方法，可以调用这些方法来将内容滚动到底部、顶部或特定点。它们可以传递持续时间，以便平稳过渡而不是立即更改位置。
> 
> 滚动事件（ionScroll）默认情况下对内容进行禁用，因为性能问题。但是，可以通过将scrollEvents设置为true来启用它们。在监听任何滚动事件之前，必须这样做。
>
> *来自 Bing AI*

  [1]: https://ionicframework.com/docs/api/content#scrollevents
  [2]: ./4101364345.webp