# Asciidoctor build targets for GCP Data Lake documentation

ASCIIDOCTOR      = asciidoctor
ASCIIDOCTOR_PDF  = asciidoctor-pdf
ASCIIDOCTOR_DIAGRAM = asciidoctor-diagram
SOURCE           = book.adoc

BUILD_DIR        = build
HTML_OUT         = $(BUILD_DIR)/index.html
PDF_OUT          = $(BUILD_DIR)/gcp-data-lake.pdf

# Common flags
COMMON_FLAGS = \
  -a toc \
  -a icons=font \
  -a source-highlighter=rouge

# PDF-specific flags
PDF_FLAGS = \
  -a pdf-page-size=A4 \
  -a pdf-page-margin="[20mm,15mm,20mm,15mm]"

.PHONY: all html pdf clean

all: html pdf

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

$(BUILD_DIR):
	mkdir -p $(BUILD_DIR)

clean:
	rm -rf $(BUILD_DIR)
	@echo "Build artifacts removed."
