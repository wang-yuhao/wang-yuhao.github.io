---
layout: blog
title: 14-Latex
date: 2020-06-22 11:39:10
tags:
---

#### 1.If you can not use `alphadin.bst` in your TexStudio:
save `alphadin.bst` file from [here](https://ctan.org/tex-archive/biblio/bibtex/contrib/german/din1505) to your local device, and keep it with the same file with your .tex file.
```
	\bibliographystyle{alphadin}
	\bibliography{Bib/stud77.bib}
```

Or you can change the file path in `\bibliographystyle\{%.bst file path\%}`.

