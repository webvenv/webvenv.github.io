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

  if File.exist?(filepath)
    puts "Post already exists: #{filepath}"
    return
  end

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
  abort 'Usage: rake post title="your-title"' unless args[:title]
  create_post(POSTS, args[:title])
end

desc 'Create a new draft'
task :draft, [:title] do |_, args|
  abort 'Usage: rake draft title="your-title"' unless args[:title]
  create_post(DRAFTS, args[:title])
end

