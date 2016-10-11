# Cyrus SASL library
# v. 2.1.24
# This CMakeLists.txt file is composed by Dmitry Aksenkin

CMAKE_MINIMUM_REQUIRED(VERSION 2.6.4)
PROJECT (libsasl)
SET(CONFIG_FILE_PATH)

SET (INC_DIRECTORIES . ../../include/sasl)

IF(WIN32)
	SET(CONFIG_FILE_PATH win32)
ELSEIF(NOT APPLE)
	SET(CONFIG_FILE_PATH linux)
ELSE(UNIX AND NOT APPLE)
	SET(CONFIG_FILE_PATH mac)
ENDIF(WIN32)

LIST(APPEND INC_DIRECTORIES ../../include/sasl/${CONFIG_FILE_PATH})

INCLUDE_DIRECTORIES( ${INC_DIRECTORIES} )

SET(HEADERS ../../include/sasl/${CONFIG_FILE_PATH}/config.h
            ./plugins/plugin_common.h
            ../../include/sasl/exits.h
            ../../include/sasl/hmac-md5.h
            ../../include/sasl/md5global.h
            ../../include/sasl/sasl.h
            ../../include/sasl/saslutil.h
            ../../include/sasl/gai.h
            ../../include/sasl/md5.h
            ../../include/sasl/prop.h
            ../../include/sasl/saslplug.h
)
SET(SOURCES ./lib/auxprop.c
            ./lib/canonusr.c
            ./lib/checkpw.c
            ./lib/client.c
            ./lib/common.c
            ./lib/config.c
            ./lib/external.c
            ./lib/md5.c
            ./lib/saslutil.c
            ./lib/server.c
            ./lib/seterror.c
            ./lib/getsubopt.c
            ./plugins/plugin_common.c )
IF(WIN32)
	LIST(APPEND SOURCES ./lib/windlopen.c )
ELSEIF(UNIX AND NOT APPLE)
	LIST(APPEND SOURCES ./lib/dlopen.c )
ENDIF(WIN32)



ADD_LIBRARY(libsasl ${SASL_LIBRARY_TYPE} ${HEADERS} ${SOURCES})
SET( LINK_LIBRAIES )

IF(WIN32)
	LIST(APPEND LINK_LIBRAIES ws2_32.lib)
ELSEIF(UNIX AND NOT APPLE)
	LIST(APPEND LINK_LIBRAIES resolv)
ENDIF(WIN32)

TARGET_LINK_LIBRARIES(libsasl ${LINK_LIBRAIES})

INSTALL(TARGETS libsasl
	RUNTIME DESTINATION bin
	LIBRARY DESTINATION bin
	ARCHIVE DESTINATION lib
)
