# set of openLDAP static libraries

CMAKE_MINIMUM_REQUIRED(VERSION 2.6.4)
PROJECT (openldap)

FIND_PACKAGE(openssl REQUIRED)

MESSAGE("OPENSSL_VERSION: ${OPENSSL_VERSION}")

SET(INC_DIRECTORIES include include/sasl)

SET(SOURCES )

SET(SASL_LIBRARY_TYPE STATIC)
#SET(SASL_LIBRARY_TYPE SHARED)

IF("${SASL_LIBRARY_TYPE}" STREQUAL "STATIC")
	#MESSAGE ("---- Static type of SASL library will be created")
	ADD_DEFINITIONS( -DLIBSASL_STATICAL_LINKAGE )
ELSE("${SASL_LIBRARY_TYPE}" STREQUAL "STATIC")
	ADD_DEFINITIONS(-DLIBSASL_EXPORTS)
	#MESSAGE ("Shared type of SASL library will be created")
ENDIF("${SASL_LIBRARY_TYPE}" STREQUAL "STATIC")

ADD_DEFINITIONS(
	-Dopenldap_EXPORTS
	-DLDAP_LIBS_DYNAMIC
)

#Windows additional directories
IF(WIN32)
	LIST(APPEND INC_DIRECTORIES
		include/win32
	)
ELSEIF(UNIX AND NOT APPLE)
	#Linux additional directories
	LIST(APPEND INC_DIRECTORIES include/sasl/linux)
	INCLUDE_DIRECTORIES( include/linux SYSTEM)
ELSEIF(APPLE)
	#Mac OS X additional directories
	LIST(APPEND INC_DIRECTORIES include/sasl/mac)
	LIST(APPEND INC_DIRECTORIES include/mac)
ENDIF(WIN32)

INCLUDE_DIRECTORIES( ${INC_DIRECTORIES} )

AUX_SOURCE_DIRECTORY(libraries/liblber liblber_SOURCES)
AUX_SOURCE_DIRECTORY(libraries/libldap libldap_SOURCES)

LIST(APPEND SOURCES
	${liblber_SOURCES}
	${libldap_SOURCES}
)

#remove windows dependent file
IF(UNIX)
	LIST(REMOVE_ITEM SOURCES libraries/liblber/nt_err.c)
ELSEIF(WIN32)
	#remove unix dependent file
	LIST(REMOVE_ITEM SOURCES libraries/libldap/dlopen.c)
ENDIF(UNIX)

#additional libraries that are part of openLDAP
ADD_SUBDIRECTORY(libraries/libldif)
ADD_SUBDIRECTORY(libraries/liblutil)
ADD_SUBDIRECTORY(libraries/libsasl)

ADD_LIBRARY (openldap SHARED
	${SOURCES}
	include/lber.h
	include/ldap_cdefs.h
)

SET_TARGET_PROPERTIES(openldap PROPERTIES LINKER_LANGUAGE C)

# set not incremental linkage for Windows
IF(WIN32)
	SET_TARGET_PROPERTIES(openldap PROPERTIES LINK_FLAGS_DEBUG /INCREMENTAL:NO)
	SET_TARGET_PROPERTIES(openldap PROPERTIES LINK_FLAGS_RELEASE /INCREMENTAL:NO)
ENDIF(WIN32)

# set output name of shared library
SET_TARGET_PROPERTIES(
	openldap
	PROPERTIES
	RELEASE_OUTPUT_NAME   openldap
	DEBUG_OUTPUT_NAME   openldap
)

TARGET_LINK_LIBRARIES(openldap
	libldif
	liblutil
	libsasl
	${OPENSSL_LIBRARIES})

IF(WIN32)
	SET(LIBDEST bin)
ELSE()
	SET(LIBDEST lib)
ENDIF()

INSTALL(TARGETS openldap
	RUNTIME DESTINATION bin
	LIBRARY DESTINATION ${LIBDEST}
	ARCHIVE DESTINATION lib
)
INSTALL(DIRECTORY include DESTINATION .)
