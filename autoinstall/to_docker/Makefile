UBUNTU_VERSION_MJR = 22
UBUNTU_VERSION_MDL = 04
UBUNTU_VERSION_MNR = 3
TARGET_MACHINE_HN = GPU01
TARGET_MACHINE_IP = 192.168.0.11


ISO_FILENAME = ubuntu-$(UBUNTU_VERSION_MJR).$(UBUNTU_VERSION_MDL).$(UBUNTU_VERSION_MNR)-live-server-amd64.iso
ISO_URL = https://releases.ubuntu.com/$(UBUNTU_VERSION_MJR).$(UBUNTU_VERSION_MDL)/$(ISO_FILENAME)

ISO_ROOT = iso_root

CONFIG_DIR = config

GRUBCFG_PATH = boot/grub/grub.cfg
# USERDATA_BASE = user-data
USERDATA_FILENAME = user-data
METADATA_FILENAME = meta-data
NETPLAN_FILEPATH = post-processing/00-installer-config.yaml
AUTHKEY_FILEPATH = post-processing/authorized_keys_ansible

GENISO_LABEL = MYUBISOIMG
GENISO_FILENAME = ubuntu22043-GPU04.iso
GENISO_BOOTIMG = boot/grub/i386-pc/eltorito.img
GENISO_BOOTCATALOG = /boot.catalog
GENISO_SECTOR_HEAD = $(shell fdisk -l $(ISO_FILENAME) | grep iso2 | cut -d' ' -f2)
GENISO_SECTOR_LAST = $(shell fdisk -l $(ISO_FILENAME) | grep iso2 | cut -d' ' -f3)


all:
	$(MAKE) extract-isoorg
	$(MAKE) setup-iso
	$(MAKE) generate-iso

.PHONY: extract-isoorg
extract-isoorg:
	mkdir -p $(ISO_ROOT)
	if ls ${ISO_ROOT}/* > /dev/null 2>&1; then \
        echo "Files exist"; \
    else \
        xorriso -osirrox on -indev $(ISO_FILENAME) -extract / $(ISO_ROOT); \
    fi
	ls -la

.PHONY: setup-iso
setup-iso:
	chmod 755 $(ISO_ROOT)
	chmod 644 $(ISO_ROOT)/$(GRUBCFG_PATH)
	cp -f $(CONFIG_DIR)/$(GRUBCFG_PATH) $(ISO_ROOT)/$(GRUBCFG_PATH)
	cp -f $(CONFIG_DIR)/$(USERDATA_FILENAME) $(ISO_ROOT)/
	cp -f $(CONFIG_DIR)/$(METADATA_FILENAME) $(ISO_ROOT)/
	cp -f $(CONFIG_DIR)/$(NETPLAN_FILEPATH) $(ISO_ROOT)/
	cp -f $(CONFIG_DIR)/$(AUTHKEY_FILEPATH) $(ISO_ROOT)/

.PHONY: generate-iso
generate-iso:
	fdisk -l $(ISO_FILENAME) | grep iso2 | cut -d' ' -f2
	fdisk -l $(ISO_FILENAME) | grep iso2 | cut -d' ' -f3

	xorriso -as mkisofs -volid $(GENISO_LABEL) \
	-output $(GENISO_FILENAME) \
	-eltorito-boot $(GENISO_BOOTIMG) \
	-eltorito-catalog $(GENISO_BOOTCATALOG) -no-emul-boot \
	-boot-load-size 4 -boot-info-table -eltorito-alt-boot \
	-no-emul-boot -isohybrid-gpt-basdat \
	-append_partition 2 28732ac11ff8d211ba4b00a0c93ec93b --interval:local_fs:$(GENISO_SECTOR_HEAD)d-$(GENISO_SECTOR_LAST)d::'$(ISO_FILENAME)' \
	-e '--interval:appended_partition_2_start_1782357s_size_8496d:all::' \
	--grub2-mbr --interval:local_fs:0s-15s:zero_mbrpt,zero_gpt:'$(ISO_FILENAME)' \
	"${ISO_ROOT}"
	ls -la
# end