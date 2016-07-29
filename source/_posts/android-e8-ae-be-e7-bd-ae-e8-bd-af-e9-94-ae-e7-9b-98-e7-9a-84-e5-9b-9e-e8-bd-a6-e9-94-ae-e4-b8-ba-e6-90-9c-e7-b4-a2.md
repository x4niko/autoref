---
title: Android设置软键盘的回车键为搜索
tags:
  - android
  - 搜索
  - 软键盘
id: 253
categories:
  - android
  - android应用开发
date: 2016-01-16 17:26:19
---

在布局中添加android:imeOptions="actionSearch"，也可以在代码中设置imeOptions：editText.setImeOptions(EditorInfo.IME_ACTION_SEND);

然后添加代码：
<pre>editText.setOnEditorActionListener(new OnEditorActionListener() {
	@Override
	public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
		// TODO Auto-generated method stub
		if (actionId == EditorInfo.IME_ACTION_SEARCH ||
				(event != null &amp;&amp; event.getKeyCode() == KeyEvent.KEYCODE_ENTER)){
			// do
		}
		return false;
	}
});
</pre>