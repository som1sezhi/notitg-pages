# XML-Lua syntax highlighting

Many modfiles have their code written in a strange combination of XML and Lua, with Lua code embedded within the attributes of XML tags. Thus, a regular XML syntax highlighter would paint all the Lua code the same color, which isn't super helpful for reading Lua. Some modern templates (e.g. Mirin v5) have the Lua and XML separated out into different files, allowing for regular Lua syntax highlighting to work, but the problem remains when trying to read code from files that don't use these templates.

To fix this issue, custom syntax highlighters for mixed XML-Lua have been developed for various text editors. Here are all the ones I could find on the NotITG Discord:

- [for Notepad++](https://www.dropbox.com/sh/ljw4nrcxvo5ifue/AADRiIQ1LZQOsBHBm-lPpPqKa?dl=0)
- [for Sublime Text](https://sm.heysora.net/XML-Lua.sublime-package)
- [for Vim](https://github.com/XeroOl/xml-lua.vim)
- [for VS Code](https://marketplace.visualstudio.com/items?itemName=fms-cat.xml-stepmania)

(Disclaimer: I haven't tested for myself if all of these work; the only one I have personally installed is for VS Code.)