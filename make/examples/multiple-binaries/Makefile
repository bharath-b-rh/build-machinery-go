include $(addprefix ../../, \
	golang.mk \
	targets/openshift/rpm.mk \
	targets/openshift/crd-schema-gen.mk \
	targets/openshift/deps.mk \
	targets/openshift/operator/profile-manifests.mk \
)

# Set crd-schema-gen variables
CRD_APIS :=$(addprefix ./pkg/apis/,v1 v1beta1)

# rpm wants build-id set
GO_LD_EXTRAFLAGS +=-B gobuildid

OUTPUT_DIR :=_output
CROSS_BUILD_BINDIR :=$(OUTPUT_DIR)/bin
RPM_EXTRAFLAGS :=--quiet --define 'version 2.42.0' --define 'dist .el7' --define 'release 6'

# $1 - target name
# $2 - apis
# $3 - manifests
$(call add-crd-gen,manifests,$(CRD_APIS),./manifests)

# $1 - target name
# $2 - profile patches dir
# $3 - manifests dir
$(call add-profile-manifests,manifests-1,./profile-patches-1,./profile-manifests-1)

# $1 - target name
# $2 - profile patches dir
# $3 - manifests dir
$(call add-profile-manifests,manifests-2,./profile-patches-2,./profile-manifests-2)


cross-build-darwin-amd64:
	+@GOOS=darwin GOARCH=amd64 $(MAKE) --no-print-directory build GO_BUILD_BINDIR:=$(CROSS_BUILD_BINDIR)/darwin_amd64
.PHONY: cross-build-darwin-amd64

cross-build-windows-amd64:
	+@GOOS=windows GOARCH=amd64 $(MAKE) --no-print-directory build GO_BUILD_BINDIR:=$(CROSS_BUILD_BINDIR)/windows_amd64
.PHONY: cross-build-windows-amd64

cross-build: cross-build-darwin-amd64 cross-build-windows-amd64
.PHONY: cross-build

clean-cross-build:
	$(RM) -r '$(CROSS_BUILD_BINDIR)'
	if [ -d '$(OUTPUT_DIR)' ]; then rmdir --ignore-fail-on-non-empty '$(OUTPUT_DIR)'; fi
.PHONY: clean-cross-build

clean: clean-cross-build
