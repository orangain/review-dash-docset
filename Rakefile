# review-dash-docset
# Copyright (C) 2015 orangain
#
# This library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public
# License along with this library; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301
# USA

require 'erb'
require 'rest_client'
require "sqlite3"

task :default => :build
task :build => [:render_html, :extract_indexes]

MARKDOWN_PATH = 'review.docset/Contents/Resources/Documents/format.md'
TEMPLATE_PATH = 'template.erb'
HTML_PATH = 'review.docset/Contents/Resources/Documents/format.html'
DB_PATH = 'review.docset/Contents/Resources/docSet.dsidx'

task :render_html do
  markdown_body = open(MARKDOWN_PATH).read
  html_body = RestClient.post('https://api.github.com/markdown/raw', markdown_body, content_type: 'text/x-markdown')

  FileUtils.mkdir_p(File.dirname(HTML_PATH))

  open(HTML_PATH, 'w') do |f|
    f << ERB.new(open(TEMPLATE_PATH).read).result(binding).gsub(/id="user-content-/, 'id="')
  end
end

task :extract_indexes do
  indexes = []
  open(HTML_PATH).each_line.grep(/<\/h2>/) do |line|
    line.match(/href="(?<href>.*?)".*<\/a>(?<name>.*?)<\/h2>/) do |m|
      indexes.push({
        name: m[:name],
        type: 'Section',
        path: "format.html#{m[:href]}",
      })
    end
  end

  File.unlink DB_PATH if File.exist? DB_PATH
  db = SQLite3::Database.new(DB_PATH)
  db.execute 'CREATE TABLE searchIndex(id INTEGER PRIMARY KEY, name TEXT, type TEXT, path TEXT);'
  db.execute 'CREATE UNIQUE INDEX anchor ON searchIndex (name, type, path);'
  indexes.each do |index|
    db.execute 'INSERT OR IGNORE INTO searchIndex(name, type, path) VALUES (?, ?, ?)',
      index[:name], index[:type], index[:path]
  end
end
