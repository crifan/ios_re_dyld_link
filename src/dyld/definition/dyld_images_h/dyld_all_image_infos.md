# dyld_all_image_infos

* `dyld_all_image_infos`
  * = `struct dyld_all_image_infos`

## `struct dyld_all_image_infos`的定义

* `struct dyld_all_image_infos`
  * 定义
    * `dyld/dyld-852.2/include/mach-o/dyld_images.h`
      * `struct dyld_all_image_infos`
        * 旧：`struct __attribute__((aligned(16))) dyld_all_image_infos`
    * libdyld/dyld_process_info_internal.h
      * `struct dyld_all_image_infos_32`
      * `struct dyld_all_image_infos_64`

### mach-o/dyld_images.h

[mach-o/dyld_images.h](https://opensource.apple.com/source/dyld/dyld-852.2/include/mach-o/dyld_images.h.auto.html)

```c
struct dyld_all_image_infos {
	uint32_t						version;		/* 1 in Mac OS X 10.4 and 10.5 */
	uint32_t						infoArrayCount;
#if defined(__cplusplus) && (BUILDING_LIBDYLD || BUILDING_DYLD)
    std::atomic<const struct dyld_image_info*>	infoArray;
#else
    const struct dyld_image_info*    infoArray;
#endif
	dyld_image_notifier				notification;		
	bool							processDetachedFromSharedRegion;
	/* the following fields are only in version 2 (Mac OS X 10.6, iPhoneOS 2.0) and later */
	bool							libSystemInitialized;
	const struct mach_header*		dyldImageLoadAddress;
	/* the following field is only in version 3 (Mac OS X 10.6, iPhoneOS 3.0) and later */
	void*							jitInfo;
	/* the following fields are only in version 5 (Mac OS X 10.6, iPhoneOS 3.0) and later */
	const char*						dyldVersion;
	const char*						errorMessage;
	uintptr_t						terminationFlags;
	/* the following field is only in version 6 (Mac OS X 10.6, iPhoneOS 3.1) and later */
	void*							coreSymbolicationShmPage;
	/* the following field is only in version 7 (Mac OS X 10.6, iPhoneOS 3.1) and later */
	uintptr_t						systemOrderFlag;
	/* the following field is only in version 8 (Mac OS X 10.7, iPhoneOS 3.1) and later */
	uintptr_t						uuidArrayCount;
	const struct dyld_uuid_info*	uuidArray;		/* only images not in dyld shared cache */
	/* the following field is only in version 9 (Mac OS X 10.7, iOS 4.0) and later */
	struct dyld_all_image_infos*	dyldAllImageInfosAddress;
	/* the following field is only in version 10 (Mac OS X 10.7, iOS 4.2) and later */
	uintptr_t						initialImageCount;
	/* the following field is only in version 11 (Mac OS X 10.7, iOS 4.2) and later */
	uintptr_t						errorKind;
	const char*						errorClientOfDylibPath;
	const char*						errorTargetDylibPath;
	const char*						errorSymbol;
	/* the following field is only in version 12 (Mac OS X 10.7, iOS 4.3) and later */
	uintptr_t						sharedCacheSlide;
	/* the following field is only in version 13 (Mac OS X 10.9, iOS 7.0) and later */
	uint8_t							sharedCacheUUID[16];
	/* the following field is only in version 15 (macOS 10.12, iOS 10.0) and later */
	uintptr_t						sharedCacheBaseAddress;
#if defined(__cplusplus) && (BUILDING_LIBDYLD || BUILDING_DYLD)
    // We want this to be atomic in libdyld so that we can see updates when we map it shared
    std::atomic<uint64_t>           infoArrayChangeTimestamp;
#else
	uint64_t						infoArrayChangeTimestamp;
#endif
	const char*						dyldPath;
	mach_port_t						notifyPorts[DYLD_MAX_PROCESS_INFO_NOTIFY_COUNT];
#if __LP64__
	uintptr_t						reserved[13-(DYLD_MAX_PROCESS_INFO_NOTIFY_COUNT/2)];
#else
	uintptr_t						reserved[13-DYLD_MAX_PROCESS_INFO_NOTIFY_COUNT];
#endif
	/* the following field is only in version 16 (macOS 10.13, iOS 11.0) and later */
    uintptr_t                       compact_dyld_image_info_addr;
    size_t                          compact_dyld_image_info_size;
    uint32_t                        platform; // FIXME: really a dyld_platform_t, but those aren't exposed here.

    /* the following field is only in version 17 (macOS 10.16) and later */
    uint32_t                          aotInfoCount;
    const struct dyld_aot_image_info* aotInfoArray;
    uint64_t                          aotInfoArrayChangeTimestamp;
    uintptr_t                         aotSharedCacheBaseAddress;
    uint8_t                           aotSharedCacheUUID[16];
};
```


### dyld_process_info_internal.h

* struct dyld_all_image_infos_32
* struct dyld_all_image_infos_64

->

* libdyld/dyld_process_info_internal.h

```c
struct dyld_all_image_infos_32 {
    uint32_t                        version;
    uint32_t                        infoArrayCount;
    std::atomic<uint32_t>           infoArray;
    uint32_t                        notification;
    bool                            processDetachedFromSharedRegion;
    bool                            libSystemInitialized;
    uint32_t                        dyldImageLoadAddress;
    uint32_t                        jitInfo;
    uint32_t                        dyldVersion;
    uint32_t                        errorMessage;
    uint32_t                        terminationFlags;
    uint32_t                        coreSymbolicationShmPage;
    uint32_t                        systemOrderFlag;
    uint32_t                        uuidArrayCount;
    uint32_t                        uuidArray;
    uint32_t                        dyldAllImageInfosAddress;
    uint32_t                        initialImageCount;
    uint32_t                        errorKind;
    uint32_t                        errorClientOfDylibPath;
    uint32_t                        errorTargetDylibPath;
    uint32_t                        errorSymbol;
    uint32_t                        sharedCacheSlide;
    std::array<uint8_t, 16>         sharedCacheUUID;
    uint32_t                        sharedCacheBaseAddress;
    std::atomic<uint64_t>           infoArrayChangeTimestamp;
    uint32_t                        dyldPath;
    uint32_t                        notifyMachPorts[8];
    uint32_t                        reserved;
    uint64_t                        sharedCacheFSID;
    uint64_t                        sharedCacheFSObjID;
    uint32_t                        compact_dyld_image_info_addr;
    uint32_t                        compact_dyld_image_info_size;
    uint32_t                        platform;
    // the aot fields below will not be set in the 32 bit case
    uint32_t                        aotInfoCount;
    std::atomic<uint64_t>           aotInfoArray;
    uint64_t                        aotInfoArrayChangeTimestamp;
    uint64_t                        aotSharedCacheBaseAddress;
    std::array<uint8_t, 16>         aotSharedCacheUUID[16];
};


struct dyld_all_image_infos_64 {
    uint32_t                version;
    uint32_t                infoArrayCount;
    std::atomic<uint64_t>   infoArray;
    uint64_t                notification;
    bool                    processDetachedFromSharedRegion;
    bool                    libSystemInitialized;
    uint32_t                paddingToMakeTheSizeCorrectOn32bitAndDoesntAffect64b; // NOT PART OF DYLD_ALL_IMAGE_INFOS!
    uint64_t                dyldImageLoadAddress;
    uint64_t                jitInfo;
    uint64_t                dyldVersion;
    uint64_t                errorMessage;
    uint64_t                terminationFlags;
    uint64_t                coreSymbolicationShmPage;
    uint64_t                systemOrderFlag;
    uint64_t                uuidArrayCount;
    uint64_t                uuidArray;
    uint64_t                dyldAllImageInfosAddress;
    uint64_t                initialImageCount;
    uint64_t                errorKind;
    uint64_t                errorClientOfDylibPath;
    uint64_t                errorTargetDylibPath;
    uint64_t                errorSymbol;
    uint64_t                sharedCacheSlide;
    std::array<uint8_t, 16> sharedCacheUUID;
    uint64_t                sharedCacheBaseAddress;
    std::atomic<uint64_t>   infoArrayChangeTimestamp;
    uint64_t                dyldPath;
    uint32_t                notifyMachPorts[8];
    uint64_t                reserved[7];
    uint64_t                sharedCacheFSID;
    uint64_t                sharedCacheFSObjID;
    uint64_t                compact_dyld_image_info_addr;
    uint64_t                compact_dyld_image_info_size;
    uint32_t                platform;
    uint32_t                aotInfoCount;
    std::atomic<uint64_t>   aotInfoArray;
    uint64_t                aotInfoArrayChangeTimestamp;
    uint64_t                aotSharedCacheBaseAddress;
    std::array<uint8_t, 16> aotSharedCacheUUID[16];
};
```
