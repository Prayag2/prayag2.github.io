+++
date = '2026-06-30T17:00:00+00:00'
draft = false
title = 'GSoC - Working on Marknote'
author = "Prayag Jain"
hideFromRSS = false
+++

# Overview
I'm working on Marknote to introduce a block-editor inspired by popular note taking apps such as Notion. A block editor is a modern text editor that allows you to insert elements such as paragraphs, lists, tables, code, block quotes, etc. into separate blocks. Each block can be reordered, edited, and deleted. Such editors also give you the ability to insert elements by pressing "/" followed by the name of the element. This block based design of these editors make them very fun and intuitive to use.  
  
A problem that most of these editors have in common is incomplete markdown support. They do not support the full [commonmark](https://commonmark.org/) spec. This means many things like nested blockquotes are not possible to use. Other problems include privacy concerns due to their closed sourced nature. They also are mostly web-based applications so they're slow. This is where Marknote comes in. Marknote already is a popular note taking app by KDE. By introducing a robust block editor, users will be able to have a similar experience in an app that integrates beautifully with the KDE ecosystem, is offline, and most importantly respects privacy.

# Current Status
GSoC's coding period began on 25th May 2026. Unfortunately, my university exams began at the same time and ended on 11th June 2026. So I was not available for the first few weeks. I was still able to implement a big portion of the project.

## Integrating md4qt
I spent the first few days making md4qt a dependency of Marknote. It involved adding it into CI images, creating a KDE Craft blueprint, updating the flatpak config as well, and finally making sure it builds correctly using CMake. 

## Building Markdown Model
After md4qt was finally usable in Marknote, I started working on developing the `AsyncDocBuilder`. This class allows Marknote to read a markdown file asynchronously (without blocking the UI), and emit a signal with an Abstract Syntax Tree (AST) which represents the parsed markdown document.  

After that, I had to implement a tree model that represents the AST. The tree model inherits from `QAbstractItemModel`. It uses a wrapper class called the `TreeItem` that wraps around the AST. This represents a simple tree data structure where each node can have more than 1 children nodes. I was able to implement a working model succesfully, although it required a lot of brainstorming.

## Getting Data to QML
The tree model represented elements in the parsed markdown document. Each element may have different properties. For example, headings have a `level`, lists have a `listType`, etc. Since this model will be used in QML, the data must be accessible in QML. For that, I wrote multiple helper functions that would extract data from each node and insert them into a `QVariantMap`. This map would be accessible from the `blockData` role of the tree model, allowing me to easily access properties about these nodes in QML.

## Implementing Delegates
Now that the model is ready to use, we need QML delegates that will actually be rendered on screen. The biggest challenge here is that this model represents a tree. This means some elements can be present inside other elements. The standard built-in `TreeView` would not work here. The reason is that, instead of truly nesting QML elements, it uses indents to show a tree like structure. This is fine for things like file trees, but not for a block editor. In a markdown compliant block editor, the QML elements must truly be nested. To tackle this problem, I had to brainstorm for a week. I tried multiple techniques. One was using a Repeater where the Delegates call themselves, recursively. While this worked, it wasn't compatible with the tree model that we have created. After a bunch of research, I found out about `DelegateModel`. It acts as a filter for models in QML. So I could just define a root index for the current view without modifying the actual model. So recursion was finally compatible with the existing tree model.  

This step is still under progress. You can see in the attached video that basic markdown elements can be rendered. These are not editable yet, but markdown parsing and rendering works flawlessly.

{{< video src="video.webm" >}}

# Conclusion
Working on the block editor this month has been very exciting and challenging. There were some totally new problems that I had to solve that I didn't foresee submitting my proposal. As always, this blog post does not involve the use of AI. All words are my own so it may contain grammatical errors :P
