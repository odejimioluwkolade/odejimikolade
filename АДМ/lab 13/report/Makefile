DOCNAME = lab13_nfs_report

PDF   = $(DOCNAME).pdf
DOCX  = $(DOCNAME).docx

MD    = $(DOCNAME).md
BIB   = bib/cite.bib
CSL   = pandoc/csl/gost-r-7-0-5-2008-numeric.csl

PANDOC_FLAGS = \
	--pdf-engine=xelatex \
	--bibliography=$(BIB) \
	--csl=$(CSL) \
	--highlight-style=tango \
	-V geometry:a4paper \
	-V geometry:margin=2cm \
	--number-sections

all: pdf docx

pdf: $(PDF)
docx: $(DOCX)

$(PDF): $(MD) $(BIB) $(CSL)
	pandoc $(PANDOC_FLAGS) -o $@ $<

$(DOCX): $(MD) $(BIB) $(CSL)
	pandoc $(PANDOC_FLAGS) -o $@ $<

clean:
	rm -f $(PDF) $(DOCX)

.PHONY: all pdf docx clean
