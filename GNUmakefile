PKGNAME := $(shell sed -n "s/Package: *\([^ ]*\)/\1/p" DESCRIPTION)
PKGVERS := $(shell sed -n "s/Version: *\([^ ]*\)/\1/p" DESCRIPTION)
PKGSRC  := $(shell basename $(PWD))
TGZ     := $(PKGNAME)_$(PKGVERS).tar.gz

# Specify the directory holding R binaries. To use an alternate R build (say a
# pre-prelease version) use `make RBIN=/path/to/other/R/` or `export RBIN=...`
# If no alternate bin folder is specified, the default is to use the folder
# containing the first instance of R on the PATH.
RBIN ?= $(shell dirname "`which R`")

README.html: README.md
	"$(RBIN)/Rscript" -e "rmarkdown::render('README.md', output_format = 'html_document')"

build:
	"$(RBIN)/R" CMD build .

install: build
	"$(RBIN)/R" CMD INSTALL $(TGZ)

check: build
	"$(RBIN)/R" CMD check --as-cran $(TGZ)

vignettes/%.html: vignettes/%.Rmd
	"$(RBIN)/Rscript" -e "tools::buildVignette(file = 'vignettes/$*.Rnw', dir = 'vignettes')"

vignettes: vignettes/chemCal.html

pd:
	"$(RBIN)/Rscript" -e "pkgdown::build_site()"
	cp vignettes/chemCal.pdf docs/articles
	git add -A
	git commit -m 'Static documentation rebuilt by pkgdown::build_site()' -e

winbuilder: build
	@echo "Uploading to R-release on win-builder"
	curl -T $(TGZ) ftp://anonymous@win-builder.r-project.org/R-release/
	@echo "Uploading to R-devel on win-builder"
	curl -T $(TGZ) ftp://anonymous@win-builder.r-project.org/R-devel/

test: install
	NOT_CRAN=true "$(RBIN)/Rscript" -e 'devtools::test()'
