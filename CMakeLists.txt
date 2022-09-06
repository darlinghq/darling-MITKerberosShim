project(darling-MITKerberosShim)

remove_sdk_framework(Kerberos)

set(DYLIB_INSTALL_NAME "/System/Library/Frameworks/Kerberos.framework/Versions/A/Libraries/libHeimdalProxy.dylib")

add_darling_library(HeimdalProxy
	heim-proxy.c
)
make_fat(HeimdalProxy)

target_link_libraries(HeimdalProxy PRIVATE
	GSS
	Heimdal

	system
)

target_link_options(HeimdalProxy PRIVATE
	-Wl,-alias_list,${CMAKE_CURRENT_SOURCE_DIR}/stubs/reexport-stubs.txt
)

set(DYLIB_COMPATIBILITY_VERSION "5.0.0")
set(DYLIB_CURRENT_VERSION "6.0.0")

set(FRAMEWORK_VERSION "A")

install(TARGETS HeimdalProxy DESTINATION libexec/darling/System/Library/Frameworks/Kerberos.framework/Versions/A/Libraries)

generate_sdk_framework(Kerberos
    VERSION ${FRAMEWORK_VERSION}
    HEADER "darling-framework/include/Kerberos"
)

add_framework(Kerberos
	CURRENT_VERSION
	VERSION ${FRAMEWORK_VERSION}
	FAT
	SOURCES
		auth_context.c
		cache.c
		ccache.c
		creds.c
		crypto.c
		dummy.c
		gic.c
		gss-glue.c
		gss-symbols.c
		keytab.c
		kll.c
		Krb4DeprecatedAPIs.c
		misc-mit.c
		misc.c
		mk_req.c
		password.c
		principal.c
		profile/prof_err.c
		profile/prof_file.c
		profile/prof_FSp_glue.c
		profile/prof_get.c
		profile/prof_init.c
		profile/prof_parse.c
		profile/prof_set.c
		profile/prof_tree.c
		rd_req.c
	DEPENDENCIES
		Heimdal
		Security
		CoreFoundation
		GSS
		HeimdalProxy

		system
)

target_link_options(Kerberos PRIVATE
	-Wl,-exported_symbols_list,${CMAKE_CURRENT_SOURCE_DIR}/exported-symbols
)

target_include_directories(Kerberos PRIVATE
	${CMAKE_CURRENT_SOURCE_DIR}/include
	${CMAKE_CURRENT_SOURCE_DIR}
)

foreach(SYM_LIB IN ITEMS com_err des425 gssapi_krb5 k5crypto krb4 krb5 krb524 krb5support)
	InstallSymlink("/System/Library/Frameworks/Kerberos.framework/Kerberos" "${CMAKE_INSTALL_PREFIX}/libexec/darling/usr/lib/lib${SYM_LIB}.dylib")
endforeach()
