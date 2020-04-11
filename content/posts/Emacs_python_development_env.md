---
title: "Emacs搭建python开发环境"
date: 2019-11-10T05:07:07+08:00
draft: false
categories: ["linux"]
tags: ["linux","emacs"]
---

### 安装python必须的依赖

`sudo pip install jedi elpy rope pylint`

### emacs配置

在`～/.emacs.d/`路径新建文件`init.el`

添加以下配置:

```
(require 'package)

(package-initialize)
;;配置包管理列表的源
 (setq package-archives '(("gnu"   . "http://elpa.emacs-china.org/gnu/")
			 ("melpa" . "https://stable.melpa.org/packages/")))

(require 'cl)


(defvar my/packages '(
		      company
		      better-deafults
		      material-theme
		      elpy
		      company-jedi
		      switch-window
		      projectile
		      neotree
		      )

  )
;;jedi配置
(setq jedi:setup-keys t)
(setq jedi:complete-on-dot t)
(setq elpy-rpc-backend "jedi")
(when (fboundp 'jedi:setup) (jedi:setup))
;; 自动补全
(global-company-mode t)
(add-hook 'after-init-hook 'global-company-mode)
;; 使用material主题
(load-theme 'material t)
;; enable python插件
(elpy-enable)
;;设置切换窗口为a-z
(setq switch-window-shortcut-style 'qwerty)
(global-set-key (kbd "C-x o") 'switch-window)
;;projectilep配置
(projectile-mode +1)
(define-key projectile-mode-map (kbd "C-c p") 'projectile-command-map)
;;neotree配置
(setq neo-smart-open t)
(setq projectile-switch-project-action 'neotree-projectile-action)
  (defun neotree-project-dir ()
    "Open NeoTree using the git root."
    (interactive)
    (let ((project-dir (projectile-project-root))
          (file-name (buffer-file-name)))
      (neotree-toggle)
      (if project-dir
          (if (neo-global--window-exists-p)
              (progn
                (neotree-dir project-dir)
                (neotree-find file-name)))
        (message "Could not find git project root."))))

```


### emacs配置

进入Emacs,`ALT-X`输入`list-packages`

安装必须的包:

```
		      company
		      better-deafults
		      material-theme
		      elpy
		      company-jedi
		      switch-window
		      projectile
		      neotree

```

解释:

```

		      company   ;;自动补全

		      better-deafults 

		      material-theme    ;;material主题

		      elpy  ;;python开发必须环境

		      company-jedi  ::jedi

		      switch-window ::快速切换窗口

		      projectile    ;;项目管理

		      neotree   ;;树形文件结构
```
