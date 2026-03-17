DOCNAME = lab13_nfs_presentation

PDF  = $(DOCNAME).pdf
HTML = $(DOCNAME).html
MD   = $(DOCNAME).md

PANDOC_FLAGS_PDF = \
	--pdf-engine=xelatex \
	-t beamer \
	--highlight-style=tango

PANDOC_FLAGS_HTML = \
	-t revealjs \
	--standalone \
	--slide-level=2 \
	--highlight-style=tango

all: pdf html

pdf: $(PDF)
html: $(HTML)

$(PDF): $(MD)
	pandoc $(PANDOC_FLAGS_PDF) -o $@ $<

$(HTML): $(MD)
	pandoc $(PANDOC_FLAGS_HTML) -o $@ $<

clean:
	rm -f $(PDF) $(HTML)

.PHONY: all pdf html clean
