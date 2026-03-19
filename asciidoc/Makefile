# Asciidoctor build targets for GCP Data Lake documentation

ASCIIDOCTOR         = asciidoctor
ASCIIDOCTOR_PDF     = asciidoctor-pdf
ASCIIDOCTOR_DIAGRAM = asciidoctor-diagram
ASCIIDOCTOR_REDUCER = asciidoctor-reducer
PANDOC              = pandoc
SOURCE              = book.adoc

BUILD_DIR        = build
HTML_OUT         = $(BUILD_DIR)/index.html
PDF_OUT          = $(BUILD_DIR)/gcp-data-lake.pdf
GFM_OUT          = $(BUILD_DIR)/gcp-data-lake.md
DOCBOOK_TMP      = $(BUILD_DIR)/.book.xml
FLAT_TMP         = $(BUILD_DIR)/.book_flat.adoc

# Common flags
COMMON_FLAGS = \
  -a toc \
  -a icons=font \
  -a source-highlighter=rouge

# PDF-specific flags
PDF_FLAGS = \
  -a pdf-page-size=A4 \
  -a pdf-page-margin="[20mm,15mm,20mm,15mm]"

.PHONY: all html pdf gfm clean

all: html pdf gfm

html: $(BUILD_DIR)
	$(ASCIIDOCTOR) $(COMMON_FLAGS) \
	  -r $(ASCIIDOCTOR_DIAGRAM) \
	  -a imagesdir=diagrams \
	  --destination-dir $(BUILD_DIR) \
	  --out-file index.html \
	  $(SOURCE)
	@echo "HTML output: $(HTML_OUT)"

pdf: $(BUILD_DIR)
	$(ASCIIDOCTOR_PDF) $(COMMON_FLAGS) $(PDF_FLAGS) \
	  -r $(ASCIIDOCTOR_DIAGRAM) \
	  -a imagesdir=diagrams \
	  --destination-dir $(BUILD_DIR) \
	  --out-file gcp-data-lake.pdf \
	  $(SOURCE)
	@echo "PDF output: $(PDF_OUT)"

# GitHub Flavoured Markdown
# Pipeline: asciidoctor-reducer (flatten includes) → asciidoctor docbook5 → pandoc gfm
gfm: $(BUILD_DIR)
	$(ASCIIDOCTOR_REDUCER) $(SOURCE) -o $(FLAT_TMP)
	$(ASCIIDOCTOR) --backend docbook5 \
	  -a imagesdir=diagrams \
	  -o $(DOCBOOK_TMP) \
	  $(FLAT_TMP)
	$(PANDOC) --from docbook --to gfm --wrap=none \
	  -o $(GFM_OUT) \
	  $(DOCBOOK_TMP)
	@echo "GFM output: $(GFM_OUT)"

$(BUILD_DIR):
	mkdir -p $(BUILD_DIR)

clean:
	rm -rf $(BUILD_DIR)
	@echo "Build artifacts removed."
