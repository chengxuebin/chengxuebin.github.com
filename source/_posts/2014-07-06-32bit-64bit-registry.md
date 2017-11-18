---
layout: post
title: "32位和64位程序的注册表知识点"
category: "windows"
tags: ["registry"]
---


众所周知，注册表是windows系统的数据库，系统本身以及安装的程序都依赖注册表。当windows进化到64位，还要兼容大量的32位老程序，便碰到了注册表的问题。这里只讨论64位系统的注册表机制。

注册表树最大可以有512级深度，通过注册表API一次可以创建32级深的键值。

为了解决64位系统遇到的问题，Windows使用了三套方案，共享（Shared）、[注册表重定向（Registry Redirector）](http://msdn.microsoft.com/en-us/library/windows/desktop/aa384232\(v=vs.85\).aspx)和[注册表反射（Registry Reflection）](http://msdn.microsoft.com/en-us/library/windows/desktop/aa384235\(v=vs.85\).aspx)。

- 共享，顾名思义，保存可以被32位和64位共同使用的注册表。
	
- 反射，这是一个早期的方案，只用于Windows Server 2008, Windows Vista, Windows Server 2003, 和Windows XP，从Windows 7 和 Windows Server 2008 R2 开始被移除。  

	它的做法是备份和同步，就是把同一份注册表保存到两个物理位置，这两个位置会分别被32位或64位程序使用。保存发生在RegCloseKey调用结束。使用 RegDisableReflectionKey和RegEnableReflectionKey方法可以禁用/启用反射机制。
	
- 重定向，主要使用的机制，它会区分32位和64位程序（Application），分别提供给他们不同的注册表物理存储位置，但会映射成同一个逻辑视图（View），这个过程对程序本身是透明的。也就是说，一个32位程序可以像在32位系统中一样来使用注册表，虽然它们在64位系统上被存储在不同的物理位置。  

	32位程序重定向的注册表存放在Wow6432Node下，例如, HKEY_LOCAL_MACHINE\Software 会被重定向到 HKEY_LOCAL_MACHINE\Software\Wow6432Node。但是，这些操作应该由系统而非程序本身来做。  
  
	当注册表包含%ProgramFiles% 或 %commonprogramfiles%时，64位系统会自动替换为 "%ProgramFiles(x86)%" 和 "%commonprogramfiles(x86)%"，同样的system32 被替换为  syswow64。具体的替换规则请阅读 [MSDN](http://msdn.microsoft.com/en-us/library/windows/desktop/aa384232\(v=vs.85\).aspx)

关于Windows系统中哪些目录使用的是哪种机制，请[更详细的列表](http://msdn.microsoft.com/en-us/library/windows/desktop/aa384253\(v=vs.85\).aspx)。

上面是系统的解决方案，如果你需要显式地访问64位注册表或32位注册表，可以使用KEY_WOW64_64KEY和KEY_WOW64_32KEY标识。
