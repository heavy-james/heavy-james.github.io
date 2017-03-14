---
layout: post
title:  Roboletric框架
description: Android 单元测试学习--Roboletric框架
category: blog
---


如果我们要在JVM上直接测试android组件、控件，直接实例化是不能够正常运行的，组件的生命周期不会自动回调，控件也不会自动填充，需要使用Roboletric框架来模拟android 环境。

* Roboletric 使用

        public class MainActivityTest {
            private ActivityController<MainActivity> mController;
            private MainActivity activity;

            @Before
            public void setUp() throws Exception {
    
            // Create new activity
            this.mController = buildActivity(MainActivity.class);
            this.activity = this.mController.create().postCreate(null).start().resume().visible().get();
            }

            @After
            public void tearDown() throws Exception {

                // Destroy activity
                this.mController.pause().stop().destroy();
                this.activity.finish();
            }

            @Test
            public void testOnCreateNotNull() {
                assertThat(this.activity).isNotNull();
            }
            
            
            @Test
            public void clickButton() {
                Button button = (Button) activity.findViewById(R.id.button);
                assertNotNull("test button could not be found", button);
                assertTrue("button does not contain text 'Click Me!'", "Click Me".equals(button.getText()));
                }
            }




* Roboletric 原理
 大致上是利用shadow原理（代理模式），封装了android真实的类，并且拓展了原来类的一些逻辑，以解除对android运行环境的依赖。