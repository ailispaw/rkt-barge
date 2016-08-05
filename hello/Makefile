BINARY  := hello
NAME    := ailispaw/$(BINARY)
AUTHOR  := "A.I. <ailis@paw.zone>"
VERSION := 0.0.1
OS      := linux
ARCH    := amd64
ACI     := $(BINARY)-$(VERSION)-$(OS)-$(ARCH).aci

$(ACI): $(BINARY)
	acbuild begin
	acbuild set-name $(NAME)
	acbuild copy $< /bin/$<
	acbuild set-exec /bin/$<
	acbuild port add www tcp 5000
	acbuild label add version $(VERSION)
	acbuild label add arch $(ARCH)
	acbuild label add os $(OS)
	acbuild annotation add authors $(AUTHOR)
	acbuild write $@
	acbuild end

$(BINARY):
	CGO_ENABLED=0 go build -ldflags '-extldflags "-static"' -o $(@F)

fetch: $(ACI)
	rkt --insecure-options=image fetch $<

run: fetch
	sudo start-stop-daemon -S -b --exec \
		rkt -- --insecure-options=image run --port=www:5000 $(NAME):$(VERSION)

ps:
	@rkt list

ls:
	@rkt image list

clean:
	-acbuild end >/dev/null 2>&1
	$(RM) $(ACI)
	$(RM) $(BINARY)

rktclean:
	-sudo rkt stop `rkt list --no-legend | awk '{if ($$4 == "running") print $$1}'` >/dev/null 2>&1
	-sudo rkt rm `rkt list --no-legend | awk '{print $$1}'` >/dev/null 2>&1
	-rkt image rm `rkt image list --fields=id --no-legend` >/dev/null 2>&1

distclean: rktclean clean

.PHONY: fetch run ps ls clean rktclean distclean
