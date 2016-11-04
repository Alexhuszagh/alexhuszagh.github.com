---
title:  "Switching to Visual Studio Code"
date:   2016-11-03 5:32:00
categories:
 - code
 - editors
 - sublime
 - vscode
tags:
 - code
 - editors
 - sublime
 - vscode
---

Sublime text was my go-to editor, it's minimal and extendible, supporting only what you need without the bloat of other editors. It also contains powerful editing tools, such as multiple cursors, configurable editor layouts, search/replace with PCREs, dynamic file inclusion patterns, and a rich plugin ecosystem.

Although I tried Visual Studio Code [VS Code] a year ago, it was less mature than Sublime Text, lacking many essential features. Although VS Code has improved over the last year, I was reluctant to switch, since switching meant learning a new editor, new tools, and new shortcuts. The [VS Code 1.7 release](code-update), with support for Sublime and Atom keybindings, convinced me to give it another try.

![Visual Studio Code Screenshot][screenshot]

# Advantages

VS Code, despite being around for less than 2 years, already has a mature feature set and dedicated user base. Although written in Javascript, it has the speed of a native application. It has Git integration and a plugin manager by default, and the editor is visually appealing. It simplifies web development with live, embedded HTML/Markdown previews. Finally, it allows custom build targets and integrates nicely with existing debugging tools, such as GDB, allowing you to configure it anywhere from a glorified text editor to a full-featured IDE.

# Sublime Keymap

One of Sublime's most powerful features is the use of multiple cursors, allowing parallel line editing not present in editors like Visual Studio, Eclipse, or Netbeans. To add a cursor, press `Ctrl+Click` (`Alt+Click` on VS code). Selecting a code block and pressing `Ctrl+L` produces a new cursor on each line in the selection block. Selecting a word and pressing `Ctrl+D` adds a new cursor at the next instance of that word. Commenting out a class by prepending `//` to each line, while a tedious task in most editors, takes only a few keystrokes in Sublime Text. Sublime text-like cursors work out of the box in VS Code, giving you advanced Sublime features without relearning a new editor.

# Extensions

Part of the appeal of VS Code is its growing number of extensions. In little over a year, it has gone from a bare-bone editor with a small number of core extensions, to a highly customizable editor. My favorite extensions include:

 - **[Project Manager][project-manager]** Manage and switch to different projects with ease.
 - **[Python][python]** Adds linting, code intelligence, and formatting tools for Python code.
 - **[VSCode Icons][vscode-icons]** Add icons to file extensions.
 - **[C/C++][c-cpp]** Auto-completion and symbol searching for C/C++.

# Display

Although Sublime Text enables DPI scaling (through `dpi_scale`), it requires manual configuration, which is difficult when the rendered text is miniscule. VS Code, on the other hand, automatically scales to the preferred size, looking just as good at 98 DPI and 300 DPI.

In addition, VS Code replaces the Sublime Text Minimap with line coloring on the sidebar. Line coloring keeps the core feature of the Minimap, giving you an overview of the current document, without covering portions of it.

| **Sublime Text MiniMap**    | **Visual Studio Code SideBar** |
|:---------------------------:|:------------------------------:|
| ![Sublime MiniMap][minimap] | ![VS Code SideBar][sidebar]    |

# Issues

As much as I love VS Code, it is still missing some features I regularly use in Sublime Text:

- **Indentation Translation:** No automatic conversion to the preferred indentation style, such as when pasting text.
- **Github Flavored Markdown:** Although VS Code supports the core Markdown specification, it does not support code block syntax highlighting.
- **Notes:** Sublime Text has many quality note-taking plugins that VS Code lacks.

# Migration

Overall, I'm pleased with how seamless the transition to VS Code was: I transferred my projects, build systems, theme, Sublime settings, and wrote this blog in only a few hours. The Markdown previews let me read my blog as I write it, and the debugger integrations and embedded terminal accelerate my work in C++/Python. Finally, VS Code works seamlessly on Windows and Linux, and cloud syncing keeps my user configurations the same on all workstations. VS Code is now an excellent editor, with a fast learning curve for Sublime Text users, and plenty of room to grow.

[code-update]:      https://code.visualstudio.com/updates/v1_7
[screenshot]:       {{ site.url }}/attachments/2016-11-03-VSCode.png
[minimap]:          {{ site.url }}/attachments/2016-11-03-SublimeMinimap.png
[sidebar]:          {{ site.url }}/attachments/2016-11-03-VSCodeSideBar.png
[project-manager]:  https://marketplace.visualstudio.com/items?itemName=alefragnani.project-manager
[python]:           https://marketplace.visualstudio.com/items?itemName=donjayamanne.python
[vscode-icons]:     https://marketplace.visualstudio.com/items?itemName=robertohuertasm.vscode-iconssite
[c-cpp]:            https://marketplace.visualstudio.com/items?siteitemName=ms-vscode.cpptoolssite