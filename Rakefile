# Rakefile
require 'yaml'
require 'date'

# rake new_post["記事のタイトル"] のようにターミナルで実行します
desc "Gitブランチと投稿ファイルを新規作成します"
task :new_post, [:title] do |t, args|
  title = args[:title]
  if title.nil? || title.strip.empty?
    puts "❌ エラー: タイトルを指定してください。例: rake new_post['新しい投稿']"
    exit
  end

  # --- STEP 1: slug用の連番を計算 ---
  max_slug_number = Dir.glob("_posts/*.md").map do |f|
    begin; YAML.load_file(f)['slug'].to_i; rescue; 0; end
  end.max || 0
  new_slug_number = max_slug_number + 1

  # ファイル名に使うための文字列を生成
  filename_title = title.strip.gsub(/\s+/, '-').gsub(/[\\\/:"*?<>|]/, '')

  # --- STEP 2: Gitブランチを新規作成 ---
  # ブランチ名の形式を 'post/124' のように定義
  branch_name = "post/#{new_slug_number}"

  puts "🔄 Gitブランチを作成します: #{branch_name}"

  # 安全のため、まず 'main' ブランチに移動します (あなたのデフォルトブランチが 'master' なら変更してください)
  system("git checkout main")

  # 新しいブランチを作成して、そのブランチに移動します
  # system() はRubyからOSのコマンドを実行するメソッドです
  if !system("git checkout -b #{branch_name}")
    puts "❌ Gitブランチの作成に失敗しました。同じ名前のブランチが既に存在する可能性があります。"
    exit
  end

  puts "✅ ブランチ '#{branch_name}' を作成し、移動しました。"

  # --- STEP 3: 投稿ファイルを作成 ---
  date_stamp = Date.today.strftime('%Y-%m-%d')
  filepath = "_posts/#{date_stamp}-#{filename_title}.md"

  content = <<~HEREDOC
    ---
    layout: post
    title: "#{title}"
    categories: []
    tags: []
    slug: "#{new_slug_number}"
    date: #{Time.now.strftime('%Y-%m-%d %H:%M:%S %z')}
    author: "tmo"
    description: |
    ---

    ここに本文を入力してください。
  HEREDOC

  File.write(filepath, content)

  puts "✅ 新しい投稿ファイルを作成しました: #{filepath}"
  puts "🎉 準備が完了しました！"
end