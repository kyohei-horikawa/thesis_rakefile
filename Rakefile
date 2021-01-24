# -*- coding: utf-8 -*-
desc "initialize"
task :initialize do
  template = <<'EOS'
#+TITLE: 
#+AUTHOR: 
#+LANGUAGE: jp
#+OPTIONS: ^:{}
#+LATEX_HEADER:\renewcommand{\bibname}
EOS

  cover = <<'EOS'
\title{卒業論文\\タイトル}
\author{関西学院大学理工学部\\情報科学科 西谷研究室\\学籍番号 名前 }
\date{2021年3月}
\begin{document}
\maketitle
\newpage
EOS

  ref = <<'EOS'
{\small\setlength\baselineskip{10pt}
\begin{thebibliography}{9}
\bibitem{rubygems} RubyGems - https://rubygems.org/ (accessd on 20 Jan 2021).
\end{thebibliography}
}
EOS

  abstract = <<'EOS'
\begin{abstract}
\end{abstract}
EOS

  git = <<'EOS'
.DS_STORE
*~
__*
EOS

  `git clone git@github.com:kyohei-horikawa/thesis_header.git && rm -rf /thesis_header/.git && mv thesis_header header`
  `mkdir cover body abstract acknowledge reference appendix fig program pptx thesis keynote`
  `touch cover/cover.tex`
  `touch body/body.org`
  `touch appendix/appendix.org`
  `touch abstract/abstract.tex`
  `touch acknowledge/acknowledge.tex`
  `touch reference/reference.tex`
  `touch .gitignore`

  files = ["body", "appendix"] # body,appendixのヘッダー
  files.each do |target_file|
    File.open("#{target_file}/#{target_file}.org", "w") do |f|
      f.puts template
    end
  end

  File.open("cover/cover.tex", "w") do |f| #coverのテンプレ
    f.puts cover
  end

  File.open("abstract/abstract.tex", "w") do |f| #abstractのテンプレ
    f.puts abstract
  end

  File.open("acknowledge/acknowledge.tex", "w") do |f| #acknowledgeのテンプレ
    f.puts '\chapter*{謝辞}'
  end

  File.open("reference/reference.tex", "w") do |f| #referenceのテンプレ
    f.puts ref
  end

  File.open(".gitignore", "w") do |f| #gitignore
    f.puts git
  end
end

desc "mkpdf"
task :mkpdf do
  # files = ["body"]
  files = ["body", "appendix"]
  pdf_file = "thesis"

  files.each do |target_file|
    command = "emacs ./#{target_file}/#{target_file}.org --batch -f org-latex-export-to-latex --kill"
    system command # org->tex
    lines = File.readlines("./#{target_file}/#{target_file}.tex")
    lines = convert_main(lines) #tex化の際の余分を削除
    File.open("#{target_file}/#{target_file}.tex", "w") do |f|
      lines.each { |line| f.print line }
    end #余分を削除したものを新たに書き込み
  end

  appendix_lines = File.readlines("appendix/appendix.tex")
  appendix_lines.unshift("\\begin{appendix}")
  appendix_lines.push("\\end{appendix}")
  File.open("appendix/appendix.tex", "w") do |f|
    appendix_lines.each { |line| f.print line }
  end

  thesis_setup = convert_tex() #pdfの元を作成 thesis.tex
  File.open("#{pdf_file}/#{pdf_file}.tex", "w") do |f|
    thesis_setup.each { |line| f.print line }
  end

  commands = ["platex #{pdf_file}/#{pdf_file}.tex",
              "bibtex #{pdf_file}/#{pdf_file}.tex",
              "platex #{pdf_file}/#{pdf_file}.tex",
              "dvipdfmx thesis.dvi",
              "rake rmfile",
              "open thesis.pdf"]
  commands.each { |com| system com }
end

desc "rmfile"
task :rmfile do
  file = "grad_presen"
  t_file = "thesis"
  commands = ["dvi", "log", "aux", "out", "tex", "toc", "lof", "out.ps"]
  commands.each { |com| system "rm #{t_file}.#{com}" }
  system "tidy"
end

def convert_tex()
  head = <<'EOS'
\documentclass[12pt,a4]{jreport}

%setting format and calling packages
%\input{./header/header}
\input{./header/usepackage}% pieces
\input{./header/form00_style}% pieces
\input{./header/tightlist_setting}% pieces
%参考文献の設定===========================================================
\renewcommand{\bibname}{参考文献}

%表紙======================================================================
\input{./cover/cover.tex}

%概要======================================================================
\input{./abstract/abstract.tex}

%目次======================================================================
\tableofcontents
\listoffigures
\newpage

%本文======================================================================
\input{./body/body.tex}
\input{./acknowledge/acknowledge.tex}
\input{./reference/reference.tex}

%付録======================================================================
%\renewcommand{\appendix}{}
%\appendix
%\renewcommand{\thesection}{付録.}
\input{./appendix/appendix.tex}


\end{document}

EOS

  lines = [head]
  return lines
end

def convert_main(lines)
  new_line = []
  lines[31..-1].each do |line|
    new_line << line
  end

  new_line.each do |line|
    line.gsub!('\end{document}', "")
    line.gsub!('\section', '\chapter')
    line.gsub!('\subsection', '\section')
    line.gsub!('\subsubsection', '\subsection')
  end

  return new_line
end
