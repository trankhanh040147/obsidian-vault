You are an Linux Master Nerd, can you help me config my Nvim the most effective, comfortable way:
- 1. For now, I want to check current config and remove it first

- 2. Then config a lazyvim config from scartch
- After that, sync the config with my github repo dotfile

---
```
You are an Linux Master Nerd, can you help me config my Nvim the most effective, comfortable way:
- 1. For now, I want to check current config and remove it first

Here is my dir: 
vessel@vessel-pc:~$ ls -a
'!'                  Desktop/                             goland-tmp/                  .meteor/                      pot/                        tmux-client-33288.log
 ./                  disks/                               go-tmp/                      .meteor-install-started.txt   .Private@                   tmux-client-33309.log
 ../                 .docker/                             .gvm/                        .meteorsession                .profile                    .tmux.conf
 .alias              docs/                                .idea/                       minio/                        .profile.bak                tmux-config/
 Applications/       Documents/                           IdeaProjects/                minio-binaries/               Public/                     .tmux.conf.swp
 .attach_pid11931    Downloads/                           .ideavimrc                   .mongodb/                     .pyenv/                     tmux.reset.conf
 .attach_pid17304    .ecryptfs@                           index.html                   .new.swp                      .python_history             .var/
 .attach_pid210533   .elm/                                itask/                       node_modules/                 README.md                   .vim/
 .attach_pid34487    extension/                           .java/                       .node_repl_history            Screencasts/                .viminfo
 .attach_pid537263   .fabric-ca-client/                   java_error_in_goland.hprof   .npm/                         ShareRDS/                   .vimrc
 .attach_pid80911    .gitconfig                           .launchpadlib/               .npmrc                        .Signet/                    .vmware/
 .bash_history       .git-credentials                     .lesshst                     .nvm/                         Signet/                     Voiden/
 .bash_logout        .gitlab/                             libs.txt                     .obsidian/                    .Signet_dev/                .vscode/
 .bashrc             .gnome/                              LICENSE                      .offsetexplorer3/             .Signet_stg/                WebstormProjects/
 .bun/               .gnupg/                              .local/                      offsetexplorer3/              snap/                       .wget-hsts
 .cache/             go/                                  lua-5.4.7/                   opt/                          src/                        .xca/
 .cert/              go-build-tmp/                        lua-5.4.7.tar.gz             package-lock.json             .ssh/                       .yarn/
 .codex/             go-build-tmpjcef-p88887_scheme.tmp   .luarocks/                   .pam_environment              .sudo_as_admin_successful   yarn.lock
 conf/               go-cache/                            .m2/                         Pictures/                     Templates/                  .yarnrc
 .config/            .GoLand2025.1/                       .mc/                         .pki/                         test_files/                 .zen/
 .cups/              GolandProjects/                      mee/                         Postman/                      .tmux/
vessel@vessel-pc:~$ z .config/
vessel@vessel-pc:~/.config$ ls -a
 ./                      configstore/          gnome-control-center/           jgit/                 monitors.xml                 pulse/                user-dirs.locale
 ../                    'Cyber Range'/         gnome-initial-setup-done        kactivitymanagerdrc   monitors.xml~                QtProject.conf        VeraCrypt/
 appimagelauncher.cfg    dconf/                gnome-session/                  kconf_updaterc        nautilus/                   'Quản lý công việc'/   Viewer/
 autokey/                dlv/                  go/                             kdeconnect/           Nextcloud/                   session/              vi-mongo/
 autostart/              electron_pdfviewer/   goa-1.0/                        kdeglobals           'NoSQLBooster for MongoDB'/   Signet/               vlc/
 beekeeper-studio/       enchant/              google-chrome/                  keepassxc/            nvim/                       'Signet '/             Voiden/
 BraveSoftware/          eog/                  .gsd-keyboard.settings-ported   kglobalshortcutsrc    nvim.zip                    'Signet Docs'/         wireshark/
 CBR/                    eSign/                gtk-3.0/                        kwalletrc             obsidian/                    Survey/               Workspace/
 cef_user_data/          evolution/            gtk-4.0/                        lazydocker/           okularpartrc                'Survey Admin'/        .wrangler/
 Code/                   flameshot/            ibus/                           lazygit/              okularrc                     systemd/              yelp/
 com.global.signet/      fyne/                 ibus-bamboo/                    libreoffice/         'PDF viewer'/                 totem/                Yubico/
 com.global.signetdev/   gedit/                input-remapper-2/               menus/                pnpm/                        update-notifier/
 com.global.signetstg/   github-copilot/       JetBrains/                      mimeapps.list         Postman/                     user-dirs.dirs
vessel@vessel-pc:~/.config$

```