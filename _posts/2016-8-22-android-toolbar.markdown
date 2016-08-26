---
layout: post
title:  "Android ToolBar使用"
date:   2016-07-05
categories: android
---


Activity.java

    Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
    setSupportActionBar(toolbar);
    toolbar.setNavigationIcon(R.drawable.ic_keyboard_backspace_white_24dp);
    toolbar.setNavigationOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            onBackPressed();
        }
    });