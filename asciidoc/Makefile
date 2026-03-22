# Asciidoctor build targets for GCP Data Lake documentation

ASCIIDOCTOR         = asciidoctor
ASCIIDOCTOR_PDF     = asciidoctor-pdf
ASCIIDOCTOR_REDUCER = asciidoctor-reducer
PANDOC              = pandoc
PLANTUML            = plantuml
SOURCE              = book.adoc

BUILD_DIR        = build
IMAGES_DIR       = images
HTML_OUT         = $(BUILD_DIR)/index.html
PDF_OUT          = $(BUILD_DIR)/gcp-data-lake.pdf
GFM_OUT          = $(BUILD_DIR)/gcp-data-lake.md
DOCBOOK_TMP      = $(BUILD_DIR)/.book.xml
FLAT_TMP         = $(BUILD_DIR)/.book_flat.adoc

ICONS_DIR    = images/icons
FA_SVG_BASE  = https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/solid

# Font Awesome 4 name used in the docs → Font Awesome 6 solid SVG filename
FA_ICON_MAP = \
  briefcase:briefcase \
  cogs:gears \
  lightbulb:lightbulb \
  circle:circle \
  adjust:circle-half-stroke \
  check:check \
  times:xmark \
  minus:minus

ICON_PNGS = $(foreach m,$(FA_ICON_MAP),$(ICONS_DIR)/$(word 1,$(subst :, ,$(m))).png)

PUML_SOURCES = $(wildcard diagrams/*.puml)
PUML_IMAGES  = $(patsubst diagrams/%.puml,$(IMAGES_DIR)/%.png,$(PUML_SOURCES))

# Common flags
COMMON_FLAGS = \
  -a toc \
  -a icons=font \
  -a source-highlighter=rouge

# PDF-specific flags
PDF_FLAGS = \
  -a pdf-page-size=A4 \
  -a pdf-page-margin="[20mm,15mm,20mm,15mm]"

.PHONY: all html pdf gfm images download-icons clean

all: html pdf gfm

# ── Diagram images ──────────────────────────────────────────────────────────
images: $(PUML_IMAGES)

$(IMAGES_DIR)/%.png: diagrams/%.puml
	mkdir -p $(IMAGES_DIR)
	$(PLANTUML) -tpng -o $(CURDIR)/$(IMAGES_DIR) $<

# ── Font Awesome icons ──────────────────────────────────────────────────────
download-icons: $(ICON_PNGS)

$(ICONS_DIR):
	mkdir -p $(ICONS_DIR)

$(ICON_PNGS): | $(ICONS_DIR)
	@command -v rsvg-convert >/dev/null 2>&1 || \
	  { echo "rsvg-convert not found — run: apt-get install librsvg2-bin"; exit 1; }
	@fa4="$(basename $(notdir $@))"; \
	 fa6="$$(echo '$(FA_ICON_MAP)' | tr ' ' '\n' | grep "^$$fa4:" | cut -d: -f2)"; \
	 echo "  icon: $$fa4 ($$fa6.svg)"; \
	 curl -fsL "$(FA_SVG_BASE)/$$fa6.svg" | rsvg-convert -w 24 -h 24 - -o $@

# ── HTML ────────────────────────────────────────────────────────────────────
html: images download-icons $(BUILD_DIR)
	$(ASCIIDOCTOR) $(COMMON_FLAGS) \
	  -a imagesdir=images \
	  --destination-dir $(BUILD_DIR) \
	  --out-file index.html \
	  $(SOURCE)
	cp -r $(IMAGES_DIR) $(BUILD_DIR)/
	@echo "HTML output: $(HTML_OUT)"

# ── PDF ─────────────────────────────────────────────────────────────────────
pdf: images download-icons $(BUILD_DIR)
	$(ASCIIDOCTOR_PDF) $(COMMON_FLAGS) $(PDF_FLAGS) \
	  -a imagesdir=images \
	  --destination-dir $(BUILD_DIR) \
	  --out-file gcp-data-lake.pdf \
	  $(SOURCE)
	@echo "PDF output: $(PDF_OUT)"

# ── GitHub Flavoured Markdown ────────────────────────────────────────────────
# Pipeline: asciidoctor-reducer (flatten includes) → asciidoctor docbook5 → pandoc gfm
gfm: images download-icons $(BUILD_DIR)
	$(ASCIIDOCTOR_REDUCER) $(SOURCE) -o $(FLAT_TMP)
	$(ASCIIDOCTOR) --backend docbook5 \
	  -a imagesdir=images \
	  -o $(DOCBOOK_TMP) \
	  $(FLAT_TMP)
	$(PANDOC) --from docbook --to gfm --wrap=none \
	  -o $(GFM_OUT) \
	  $(DOCBOOK_TMP)
	cp -r $(IMAGES_DIR) $(BUILD_DIR)/
	@echo "GFM output: $(GFM_OUT)"

$(BUILD_DIR):
	mkdir -p $(BUILD_DIR)

clean:
	rm -rf $(BUILD_DIR)
	rm -f $(PUML_IMAGES)
	rm -rf $(ICONS_DIR)
	@echo "Build artifacts removed."
