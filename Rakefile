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
  # 既存の投稿ファイルから有効なslug番号のみを抽出し、最大値を計算します。
  # slugキーが存在しない、または数値に変換できない場合はスキップします。
  valid_slug_numbers = Dir.glob("_posts/*.txt").map do |f|
    begin
      # YAML.load_file の代わりに YAML.safe_load_file を使用し、Timeクラスを許可します
      front_matter = YAML.safe_load_file(f, permitted_classes: [Time])
      
      if front_matter && front_matter.key?('slug') && front_matter['slug'].to_s =~ /\A\d+\z/
        front_matter['slug'].to_i
      else
        nil # slugがない、または無効な場合はnilを返す
      end
    rescue Psych::SyntaxError => e
      # YAMLのパースエラーが発生した場合は警告を出し、このファイルのslugは無視します
      puts "⚠️ 警告: ファイル #{f} のYAMLフロントマターにエラーがあります。このファイルのslugは無視されます。エラー: #{e.message}"
      nil
    rescue => e
      # その他の予期せぬエラーが発生した場合も警告を出し、このファイルのslugは無視します
      puts "⚠️ 警告: ファイル #{f} の処理中に予期せぬエラーが発生しました。このファイルのslugは無視されます。エラー: #{e.message}"
      nil
    end
  end.compact # nilを取り除く

  # 有効なslug番号の中から最大値を見つける。有効なslugが一つもなければ0とする。
  max_slug_number = valid_slug_numbers.max || 0
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
  filepath = "_posts/#{date_stamp}-#{filename_title}.txt"

  content = <<~HEREDOC
    ---
    layout: post
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
