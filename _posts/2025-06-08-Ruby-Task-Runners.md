---
layout: post
title:  "Ruby Task Runners"
date:   2025-06-08 21:43:12 +0000
categories: [blog]
tags: []
---

> **This site is currently under construction.**  
> Testing is still being conducted — but feel free to explore what’s live so far!


![Forest flowers](assets/img/forest.png)

---

## Automating Jekyll Tasks with a Rakefile

Jekyll supports the use of [`rake`](https://ruby.github.io/rake/) — a task runner for Ruby — which allows you to automate repetitive actions like generating blog posts or drafts with a single command.

You do this by creating a `Rakefile` in the root of your Jekyll project. Inside it, you define *tasks* that can be run from the terminal.

---

### 📁 `Rakefile` Contents

Here’s the full example I use in this site:

```ruby
# frozen_string_literal: true

require 'yaml'
require 'fileutils'
require 'time'

POSTS = '_posts'
DRAFTS = '_drafts'

def create_post(folder, title)
  time = Time.now
  slug = title.downcase.strip.gsub(/[^\w\s-]/, '').gsub(/[[:space:]]+/, '-')
  filename = "#{time.strftime('%Y-%m-%d')}-#{slug}.md"
  filepath = File.join(folder, filename)

  return puts "Post already exists: #{filepath}" if File.exist?(filepath)

  File.open(filepath, 'w') do |file|
    file.puts <<~POST
      ---
      layout: post
      title:  "#{title}"
      date:   #{time.strftime('%Y-%m-%d %H:%M:%S %z')}
      categories: [blog]
      tags: []
      ---
    POST
  end

  puts "New post created: #{filepath}"
end

desc 'Create a new post'
task :post, [:title] do |_, args|
  abort 'Usage: rake post[title]' unless args[:title]
  create_post(POSTS, args[:title])
end

desc 'Create a new draft'
task :draft, [:title] do |_, args|
  abort 'Usage: rake draft[title]' unless args[:title]
  create_post(DRAFTS, args[:title])
end
```

---

## Running the Rakefile

Once your `Rakefile` is in place, you can use `bundle exec` to invoke your tasks.

The correct command syntax **depends on the shell you're using**. This is because of how different shells interpret characters like square brackets (`[]`), which Ruby uses to pass positional arguments to tasks.

---

### Shell-Specific Rake Syntax

| **Shell**       | **Command to Run**                     |
| --------------- | -------------------------------------- |
| **Bash**        | `bundle exec rake post[hello-world]`   |
| **Zsh**         | `bundle exec rake "post[hello-world]"` |
| **Fish**        | `bundle exec rake 'post[hello-world]'` |
| **PowerShell**  | `bundle exec rake 'post[hello-world]'` |
| **Windows CMD** | `bundle exec rake post[hello-world]`   |

In Ruby, this syntax comes from the task definition:

```ruby
task :post, [:title] do |_, args|
```

The `[:title]` array means the task expects a positional argument called `title`. When you run the command, you're essentially calling the `post` task and passing in a title like `hello-world`.

---

### Zsh Quirk: Brackets Are Special

If you're using **Zsh**, brackets are treated as potential [glob patterns](https://zsh.sourceforge.io/Doc/Release/Expansion.html#Filename-Generation), which can cause Rake to fail unless the command is quoted:

```zsh
bundle exec rake "post[hello-world]"
```

It’s a small but important detail that tripped me up the first time I tried this.

---

### Example Output

Here’s what you’ll see when it works:

```shell
$ bundle exec rake "post[hello-world]"

New post created: _posts/2025-06-08-hello-world.md
```

The new file will look like this:

```markdown
---
layout: post
title:  "hello-world"
date:   2025-06-08 21:43:12 +0000
categories: [blog]
tags: []
---
```

From there, you can start writing right away — and when you commit and push, the post will appear on your homepage.

---

### Final Takeaway

Ruby's `Rakefile` can be incredibly helpful when used inside a Jekyll project but you'll need to modify the execution command depending the shell you're using.


> If you’re using Zsh and trying to pass bracketed arguments to Rake, always quote the entire expression like "post[title]" to avoid unexpected globbing behavior.
{: .prompt-tip }
