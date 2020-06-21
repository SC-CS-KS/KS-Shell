# alias/unalias

alias和unalias用于别名的定义和删除。
当命令被读取时，Shell会展开别名定义，别名主要用于交互式Shell。

如：alias 'rm=rm -i' 指删除时，需要确认。
Shell不会做递归的别名展开，所有此定义是合法的。
----------------------------------------------------------------------------------------------------------------
#alias
alias +='pushd .'
alias -='popd'
alias ..='cd ..'
alias ...='cd ../..'
alias beep='echo -en "\007"'
alias cd..='cd ..'
alias dir='ls -l'
alias l='ls -alF'
alias la='ls -la'
alias ll='ls -l'
alias ls='ls $LS_OPTIONS'
alias ls-l='ls -l'
alias md='mkdir -p'
alias o='less'
alias rd='rmdir'
alias rehash='hash -r'
alias unmount='echo "Error: Try the command: umount" 1>&2; false'
alias you='if test "$EUID" = 0 ; then /sbin/yast2 online_update ; else su - -c "/sbin/yast2 online_update" ; fi'
----------------------------------------------------------------------------------------------------------------
修改alias
若要添加自己的alias，格式如下
alias la="ls -al --color=auto"
----------------------------------------------------------------------------------------------------------------
修改配置文件
上述命令，在用户登出后就无效了，可以用修改配置文件的办法，使每次都能够自动生效。
若要修改用户（而非全部用户）自己的alias，可以修改～/.bashrc文件
vii ~/.bashrc
再最后面加上你自己定义的alias，如alias la="ls -al --color=auto"
----------------------------------------------------------------------------------------------------------------

## alias 作用域

alias的使用就是为了减少交互shell下击键的次数。在脚本中我们通常应该使用function而不是alias。
在非交互的shell中，bash并不执行一些配置文件如/etc/profile，/etc/bashrc等。
如果非要使用系统预先定义的alias的话，可以这样调用你的脚本：
sh -l script

其实如果要将alias“传入”到子shell中去还是有办法的。
在bash下可以用环境变量BASH_ENV：
cat >my-alias <<EOF
alias ll='ls -l'
EOF

export BASH_ENV=my-alias

cat >try-alias <<EOF
#!/bin/bash
shopt -s expand_aliases
ll
EOF

chmod +x try-alias
./try-alias

在ksh下要用的环境变量是ENV


