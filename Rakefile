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
require 'nokogiri'

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
  doc = Nokogiri::HTML(open(HTML_PATH))
  doc.css('h2').each do |h2|
    a = h2.at_css('a')

    index = {
      name: h2.content.strip,
      type: 'Section',
      path: "format.html#{a['href']}",
    }
    indexes << index

    a.add_next_sibling "<a name='//apple_ref/cpp/#{index[:type]}/#{ERB::Util.url_encode(index[:name])}' class='dashAnchor'></a>"

    first_element = h2.next_element
    second_element = h2.next_element.next_element
    codes = []
    # Find inline <code>s in <p>
    if first_element.name == 'p'
      codes += first_element.css('code').map{ |code| code.content }.to_a
      #  codes << code.content
      #end
    end

    # Find <code>s in <ul> immediately after <p>
    if first_element.name == 'p' and second_element.name == 'ul'
      codes += second_element.css('code').map{ |code| code.content }.to_a
    end

    # Find <pre> blocks which is not an usage
    if first_element.name == 'pre'
      codes += first_element.at_css('code').content.each_line.to_a
    elsif first_element.name == 'p' and second_element.name == 'pre'
      codes += second_element.at_css('code').content.each_line.to_a
    end

    codes.each do |code|
      if /^\/\/(?<name>\w+)/ =~ code
        type = 'Directive'
      elsif /^@<(?<name>\w+)>/ =~ code
        type = 'Tag'
      elsif /#@(?<name>\w+)/ =~ code or /(?<name>#@#)/ =~ code
        type = 'Macro'
      else
        next
      end
      next if indexes.find_index { |i| i[:name] == name and i[:type] == type }

      indexes << {
        name: name,
        type: type,
        path: "format.html#{a['href']}",
      }
    end
  end

  open(HTML_PATH, 'w') do |f|
    f << doc.to_html
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
