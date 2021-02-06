# zshの整理をしたいんじゃ！

## 環境

```bash
cat /etc/os-release

NAME="Ubuntu"
VERSION="20.04.1 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.1 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal

```

## とりあえずインストール＆デフォルト設定

```bash
# インストール
sudo apt install zsh
# zshをデフォルトに変更
chsh -s $(which zsh)
# 再起動すると反映される
```

## .zshrcを分割管理したい

```
# 以下の内容を~/.zshrcに記載
ZSHHOME="${HOME}/.zsh.d"

if [ -d $ZSHHOME -a -r $ZSHHOME -a -x $ZSHHOME ]; then
	for i in $ZSHHOME/*; do
		[[ ${i##*/} = *.zsh]] && [\' -f $i -o -h $i \) -a -r $i]
	done
fi

# ディレクトリを作成
mkdir ~/.zsh.d
cd .zsh.d
```

## プラグインを入れていくぞ
### tmux(画面分割)
https://girigiribauer.com/tech/20200427/
https://qiita.com/chao____/items/8620c55c947864d100c8
```
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'

# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'git@github.com:user/plugin'
# set -g @plugin 'git@bitbucket.com:user/plugin'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
# ---------------------------------------------------------------------------- #
# basic
# ---------------------------------------------------------------------------- #
# prefixキーをC-tに変更する（好み）
set -g prefix C-t
# キーストロークのディレイを減らす
set -sg escape-time 0

# ウィンドウのインデックスを1から始める
set -g base-index 1

# ペインのインデックスを1から始める
setw -g pane-base-index 1

# 設定ファイルをリロードする
#bind r source-file ~/.tmux.conf \; display "Reloaded!"

# C-a*2でtmux内のプログラムにC-aを送る
bind C-t send-prefix

# \ でペインを縦に分割する
bind | split-window -h

# - でペインを横に分割する
bind - split-window -v

# Vimのキーバインドでペインを移動する
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R
bind -r C-h select-window -t :-
bind -r C-l select-window -t :+

# Vimのキーバインドでペインをリサイズする
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# 256色端末を使用する
set -g default-terminal "screen-256color"

# ステータスバーの色を設定する
set -g status-fg white
set -g status-bg black

## リフレッシュの間隔を設定する(デフォルト 15秒)
set -g status-interval 60
## ウィンドウリストの位置を中心寄せにする
set -g status-justify centre
# マウススクロールを可能に
set -g mouse on
set -g terminal-overrides 'xterm*:smcup@:rmcup@'
# tpm
set -g @tpm_plugins '              \
  tmux-plugins/tpm                 \
  tmux-plugins/tmux-yank           \
  tmux-plugins/tmux-open           \
  tmux-plugins/tmux-resurrect      \
  tmux-plugins/tmux-battery        \
  tmux-plugins/tmux-pain-control   \
'

run-shell '~/.tmux/plugins/tpm/tpm'
```
### 
### 


## 参考
- https://qiita.com/sayama0402/items/ac9e078b024c3287f99f
- http://fnwiya.hatenablog.com/entry/2015/11/03/191902
- 
- 
- 
