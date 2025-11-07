```
# Use vim keybindings in copy mode
setw -g mode-keys vi
set -g mouse on
set -s set-clipboard on

#Linux only
#set -g mouse on
bind -n WheelUpPane if-shell -F -t = "#{mouse_any_flag}" "send-keys -M" "if -Ft= '#{pane_in_mode}' 'send-keys -M' 'select-pane -t=; copy-mode -e; send-keys -M'"
bind -n WheelDownPane select-pane -t= \; send-keys -M
bind -n C-WheelUpPane select-pane -t= \; copy-mode -e \; send-keys -M
bind -T copy-mode-vi    C-WheelUpPane   send-keys -X halfpage-up
bind -T copy-mode-vi    C-WheelDownPane send-keys -X halfpage-down
bind -T copy-mode-emacs C-WheelUpPane   send-keys -X halfpage-up
bind -T copy-mode-emacs C-WheelDownPane send-keys -X halfpage-down
# To copy, left click and drag to highlight text in yellow, 
# once you release left click yellow text will disappear and will automatically be available in clibboard
# Update default binding of `Enter` to also use copy-pipe
unbind -T copy-mode-vi Enter
bind-key -T copy-mode-vi Enter send-keys -X copy-pipe-and-cancel "xclip -selection c"
bind-key -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel "xclip -in -selection clipboard"
bind-key -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel "xclip -in -selection clipboard"
#bind -T copy-mode-vi MouseDragEnd1Pane send -X copy-pipe-and-cancel 'xsel -b'

# color config
set-option -g default-terminal 'screen-254color'
set-option -g terminal-overrides ',xterm-256color:RGB'

set -g prefix ^A
set -g base-index 1              # start indexing windows at 1 instead of 0
set -g detach-on-destroy off     # don't exit from tmux when closing a session
set -g escape-time 0             # zero-out escape time delay
set -g history-limit 1000000     # increase history size (from 2,000)
set -g renumber-windows on       # renumber all windows when any window is closed
set -g set-clipboard on          # use system clipboard
set -g status-position top       # macOS / darwin style
set -g default-terminal "${TERM}"
set -g pane-active-border-style 'fg=magenta,bg=default'
#set -g pane-border-style 'fg=brightblack,bg=default

set -g @fzf-url-fzf-options '-p 60%,30% --prompt="   " --border-label=" Open URL "'
set -g @fzf-url-history-limit '2000'

# TMUX PLUGINS
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-yank'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @plugin 'fcsonline/tmux-thumbs'
set -g @plugin 'sainnhe/tmux-fzf'
set -g @plugin 'wfxr/tmux-fzf-url'
set -g @plugin 'omerxx/catppuccin-tmux' # My fork that holds the meetings script bc I'm lazy af
set -g @plugin 'omerxx/tmux-sessionx'

set -g @sessionx-bind 'o'
set -g @sessionx-x-path '~/dotfiles'
set -g @sessionx-window-height '85%'
set -g @sessionx-window-width '75%'
set -g @sessionx-zoxide-mode 'on'
set -g @sessionx-filter-current 'false'
set -g @sessionx-preview-enabled 'true'
set -g @continuum-restore 'on'
set -g @resurrect-strategy-nvim 'session'
set -g @catppuccin_window_left_separator ""
set -g @catppuccin_window_right_separator " "
set -g @catppuccin_window_middle_separator " █"
set -g @catppuccin_window_number_position "right"
set -g @catppuccin_window_default_fill "number"
set -g @catppuccin_window_default_text "#W"
set -g @catppuccin_window_current_fill "number"
set -g @catppuccin_window_current_text "#W#{?window_zoomed_flag,(),}"
set -g @catppuccin_status_modules_right "directory meetings date_time"
set -g @catppuccin_status_modules_left "session"
set -g @catppuccin_status_left_separator  " "
set -g @catppuccin_status_right_separator " "
set -g @catppuccin_status_right_separator_inverse "no"
set -g @catppuccin_status_fill "icon"
set -g @catppuccin_status_connect_separator "no"
set -g @catppuccin_directory_text "#{b:pane_current_path}"
set -g @catppuccin_meetings_text "#($HOME/.config/tmux/scripts/cal.sh)"
set -g @catppuccin_date_time_text "%H:%M"

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'

```