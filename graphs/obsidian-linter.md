---
title: obsidian linter
aliases:
- "Obsidian Linter"
- "linter"
created: 2023-07-27
date: 2023-07-31
tags:
- graph
- obsidian
---
## 关于 obsidian-linter

[obsidian-linter](https://github.com/platers/obsidian-linter) 是 Obsidian 的内容格式化工具。可以用于格式化 YAML、脚标、标题、空格、数学块与 Markdown 内容，同时可以自定义格式化规则。这意味着 obsidian-linter 不仅可以处理好「格式化」内容的本质工作，还可以用来方便地更新 Obsidian 文件的 YAML 元数据，这为高效撰写笔记提供了许多便利。但是，obsidian-linter 也并非银弹，写作时保持稳定的写作风格比完全依赖工具要重要得多。

因此，Yin 希望 obsidian-linter 可以完成以下目标：
- 格式化笔记内容
	- 使得文档内 Markdown 风格统一
	- 使得文档内空行，字符间空格风格统一
	- 使得文档内排版，尤其是中英文混排时风格统一
- 更新笔记元数据
	- 使得 YAML 风格统一
	- 自动更新 YAML 元数据

统一的 Obsidian 文档格式有利于内容在各个平台发布时的一致性，同时降低了引入 `git` 等版本管理工具的后续维护成本。接着 Yin 将按照目标配置 obsidian-linter。

## 具体设置

本节将会列出用到的具体设置项目，简要介绍并给出使用理由。由于 YAML 相关设置较为复杂，将在本文最后介绍。

### General

- Lint on save
	- 在保存时格式化
	- 启用。保证落盘时文件的一致性，并更新如最新一次编辑等元信息
- Display message on lint
	- linting 后显示改变的字符数量
	- 启用。日志功能
- Folder to ignore
	- 需要忽略的文件夹
	- 启用。建议将模板等不需要格式化与元数据更新的文件夹加入，注意每个文件夹之间使用行分隔
- YAML aliases section style
	- YAML aliases 部分风格
	- multi-line。使用多行风格
- YAML tags section style
	- YAML tags 部分风格
	- multi-line。使用多行风格
- Remove Unnecessary Escape Characters when in MultiLine Array Format
	- 多行风格的 [转义字符](entities/escape-character.md)
	- 开启。多行风格需要

### Heading

- header-increment
	- Heading levels should only increment by one level at a time
		- Heading 级别每次只提升一个级别
		- 开启。防止 Heading 错乱
	- Start Header Increment at Heading Level 2
		- 文档内 Heading 级别最小为 2
		- 开启。文档中只有标题为一级标题
- headings-start-line
	- Headings that do not start a line will have their preceding whitespace removed to make sure they get recognized as headers.
		- 去除标题标识符 `#` 前的空格
		- 开启。保持 Heading 行格式统一

### Footnote

- move-footnotes-to-the-bottom
	- 将脚注移到文档最后
	- 开启。在笔记级别的文档长度下，将脚注移到文档最后似乎是个还算合理的选择
- re-index-footnotes
	- 按照出现顺序重现排列脚注
	- 开启。如出现 bug 考虑关掉该选项

### Content

- blockquote-style
	- Makes sure the blockquote style is consistent
		- 保持 blockquote 风格一致
		- 开启，且使用 space 风格。
- convert-bullet-list-markers
	- Converts common bullet list marker symbols to markdown list markers
		- 将 bullet list 风格转为 Markdown list 风格
		- 开启
- emphasis-style
	- Makes sure the emphasis style is consistent
		- 保持强调风格一致
		- 开启，且使用 asterisk * 风格
- no-bare-urls
	- Encloses bare URLs with angle brackets except when enclosed in back ticks, square braces, or single or double quotes
		- 保证 url 都有描述内容
		- 开启。保证 url 质量
- ordered-list-style
	- Makes sure that ordered lists follow the style specified.
		- 保持有序列表风格一致，且保持 lazy
		- 开启。保存有序列表风格一致，lazy 可以减少修改字符数
- quote-style
	- Updates the quotes in the body content to be updated to the specified single and double quote styles
		- 保持 quote 完整
		- 开启。启用单 quote 风格，使用 `"` 作为标记
- remove-consecutive-list-markers
	- Removes consecutive list markers. Useful when copy-pasting list items
		- 当复制时，去除 list 标记
		- 开启。减少手动删除文字前标记的麻烦
- remove-empty-list-markers
	- Removes empty list markers
		- 移除空 list 标记
		- 开启
- remove-hyphenated-line-breaks
	- Removes hyphenated line breaks
		- 移除连接符
		- 开启
- remove-multiple-spaces
	- Removes two or more consecutive spaces
		- 移除多余空格
		- 开启
- strong-style
	- Makes sure the strong style is consisten
		- 强烈确保风格一致
		- 开启
- unordered-list-style
	- Makes sure that unordered lists follow the style specified
		- 确保无序列表风格一致
		- 开启。无序列表标示为 `-`

### Spacing

- compact-yaml
	- Removes leading and trailing blank lines in the YAML front matter
		- 去除 YAML 中的空行
		- 开启
- consecutive-blank-lines
	- There should be at most one consecutive blank line
		- 行之间只存在一个空行
		- 开启
- empty-line-around
	- Ensures that there is an empty line around things unless they start or end a document
	- 确保内容周围有空行包围
		- blockquotes。开启
		- code。开启
		- math。开启
		- table。开启
- heading-blank-lines
	- All headings have a blank line both before and after (except where the heading is at the beginning or end of the document)
		- 除开始或结尾的 heading 以外，其余 heading 均被空行包围
		- 开启。但关闭 Empty Line Between Yaml and Header
- move-math-block-indicators-to-their-own-line
	- Move all starting and ending math block indicators to their own lines
		- 将数学块换行
		- 开启
- remove-space-around-characters
	- Ensures that certain characters are not surrounded by whitespace
		- 确保字符周围的空格符合规范
		- 开启。开启全宽、CJK 字符选项
- space-after-list-markers
	- There should be a single space after list markers and checkboxes
		- 移除列表标记和内容中间的空格
		- 开启
- space-between-chinese-japanese-or-korean-and-english-or-numbers
	- Ensures that Chinese, Japanese, or Korean and English or numbers are separated by a single space
		- 中文字符串和英文字符之间应该由一个空格间隔
		- 开启

### Paste

暂不作任何配置

### YAML

- force-yaml-escape
	- Escapes the values for the specified YAML keys
		- 对于特定 YAML keys 使用引号标记
		- 开启。保持 `title`、`aliases` YAML keys 风格一致
- format-yaml-array
	- Allows for the formatting of regular yaml arrays
		- Format yaml aliases section
		- Format yaml tags section
			- 格式化 YAML aliases 和 tags 
			- 开启
		- Default yaml array section style
			- 默认 YAML array 格式
			- 开启。设置为多行
		- Force key values to be multi-line arrays
			- 强制 aliases、tags 为多行 arrays
			- 开启
- yaml-timestamp
	- Keep track of the date the file was last edited in the YAML front matter
	- 保存时更新时间。由于模板创建时会存在 created key，因此这里仅更新 modified 时间