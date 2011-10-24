# utility for openLdap

PROJECT(liblutil)


SET (INC_DIRS ./
	 ../../include
)

#Windows additional directories

IF(WIN32)
LIST(APPEND INC_DIRS ../../include/win32)
ELSEIF(UNIX AND NOT APPLE)
LIST(APPEND INC_DIRS ../../include/linux)
ELSEIF(APPLE)
LIST(APPEND INC_DIRS ../../include/mac)
ENDIF(WIN32)

INCLUDE_DIRECTORIES(
${INC_DIRS}
)

INCLUDE_DIRECTORIES(../../include/sasl SYSTEM)

SET(HEADERS
    ../../include/ac/bytes.h
    ../../include/lutil.h
    ../../include/lutil_hash.h
    ../../include/lutil_ldap.h
    ../../include/lutil_lockf.h
    ../../include/lutil_md5.h
    ../../include/lutil_sha1.h
)
SET(SOURCES entropy.c  hash.c   ntservice.c  sasl.c      utils.c
base64.c        getopt.c   lockf.c  passfile.c   sha1.c      uuid.c
csn.c           getpass.c  md5.c    passwd.c     sockpair.c
)

ADD_LIBRARY (liblutil STATIC
              ${HEADERS}
              ${SOURCES}
)

IF(STANDALONE_EXTERN_BUILD)

	INSTALL(TARGETS liblutil
		RUNTIME DESTINATION ${EXTERN_BIN_DIRECTORY}
		LIBRARY DESTINATION ${EXTERN_BIN_DIRECTORY}
		ARCHIVE DESTINATION ${EXTERN_LIB_DIRECTORY}
	)

ENDIF(STANDALONE_EXTERN_BUILD)



