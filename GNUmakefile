all:: remove-bom
all:: sets index pages

sets: $(wildcard sets/*)

sets/bem:
	echo 'Nothing to do here'

sets/%: blocks-common blocks-desktop
	make -C $@ -B

pages:
	make -C pages/

.SECONDEXPANSION:
blocks-%: $$(wildcard $$@/*)
	echo $@

blocks-common/%:
	bem create block -T decl.js \
	--force \
	-l blocks-common $(*F)

blocks-desktop/%:
	bem create block -T decl.js \
	--force \
	-l blocks-desktop $(*F)

index: index.ru.html index.en.html index.html

.PRECIOUS: %.html
%.html: %.bemjson.js %.bemhtml.js %.css %.ie.css %.js
	lib/bemjson2html.js $(*F).bemhtml.js $(*F).bemjson.js $(*F).html

index.bemjson.js : index.full.wiki
	touch $@
	node lib/wiki2bemjson.js index.full.wiki index.bemjson.js front

%.bemjson.js: %.full.wiki
	touch $@
	node lib/wiki2bemjson.js $(*F).full.wiki $@ index

%.bemhtml.js: %.deps.js
	mkdir -p $(*D)
	touch $@
	bem build \
		-l blocks-common \
		-l blocks-desktop \
		-l blocks \
		-d $*.deps.js \
		-t blocks-common/i-bem/bem/techs/bemhtml.js \
		-n $(*F) \
		-o ./

index.full.wiki:
	echo -e '== BEM BL\n\n * ((index.en.html English))\n * ((index.ru.html Русский))' > $@

index.%.full.wiki:
	rm -f index.$(*F).full.wiki
	cat index.$(*F).wiki >> index.$(*F).full.wiki
	echo -e '\n\n' >> index.$(*F).full.wiki
	node lib/blocks-list.js $(*F)

%.deps.js: %.bemdecl.js
	touch $@
	bem build \
		-l blocks-common \
		-l blocks-desktop \
		-l blocks \
		-d $*.bemdecl.js \
		-t deps.js \
		-n $(*F) \
		-o ./

%.bemdecl.js: %.bemjson.js
	node lib/bemjson2bemdecl.js $*.bemjson.js

.PRECIOUS: %.css
%.css: %.deps.js
	touch $@
	bem build \
		-l ../../blocks-common \
		-l ../../blocks-desktop \
		-l blocks \
		-d $*.deps.js \
		-t css \
		-n $(*F) \
		-o ./
	borschik -t css -i $@ -o $(@D)/_$(@F)

.PRECIOUS: %.ie.css
%.ie.css: %.css %.deps.js
	touch $@
	bem build \
		-l ../../blocks-common \
		-l ../../blocks-desktop \
		-l blocks \
		-d $*.deps.js \
		-t ie.css \
		-n $(*F) \
		-o ./
	borschik -t css -i $@ -o $(@D)/_$(@F)

.PRECIOUS: %.js
%.js: %.deps.js
	touch $@
	bem build \
		-l ../../blocks-common \
		-l ../../blocks-desktop \
		-l blocks \
		-d $*.deps.js \
		-t js \
		-n $(*F) \
		-o ./

.PHONY: remove-bom
remove-bom:
	find . -name '*.wiki' | xargs sed -i '1 s/^\xef\xbb\xbf//'
	find . -name '*.doc.js' | xargs sed -i '1 s/^\xef\xbb\xbf//'

.PHONY: all sets index