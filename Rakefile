desc "Yeni post oluşturma "
task :new_post, :title do |t, args|
  if args.title
    title = args.title
  else
    title = get_stdin("Post için bir başlık giriniz: ")
  end
  filename = "_posts/#{Time.now.strftime('%Y-%m-%d')}-#{title}.md"
  if File.exist?(filename)
    abort("Bu isimde bir dosya zaten var!")
  end
  puts "Yeni post oluşturuluyor...  #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
    post.puts "comments: true"
    post.puts "categories: "
    post.puts "---"
  end
end

