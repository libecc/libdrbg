project(
    'drbg', 'c',
    meson_version: '>=1.1.0',
    version: run_command('dunamai', 'from', 'git', '--style', 'semver', '--dirty', check: true)
             .stdout().strip(),
    license: 'BSD-3-Clause OR GPL-2.0-or-later',
    license_files: [ 'LICENSE' ],
    default_options: ['c_std=gnu99', 'warning_level=3', 'optimization=3' ],
)

drbg_build_opts = []
hash_build_opts = []

# parse options old style (i.e. w/ define on cmdline)
libdrgb_with_hash = get_option('with-hash-drbg')
if libdrgb_with_hash
    drbg_build_opts += [ '-DWITH_HASH_DRBG' ]
endif
summary('HASH DRBG', libdrgb_with_hash, section: 'libdrgb')

libdrgb_with_hmac = get_option('with-hmac-drbg')
if libdrgb_with_hmac
    drbg_build_opts += [ '-DWITH_HMAC_DRBG' ]
endif
summary('HMAC DRBG', libdrgb_with_hmac, section: 'libdrgb')

libdrgb_with_ctr = get_option('with-ctr-drbg')
if libdrgb_with_ctr
    drbg_build_opts += [ '-DWITH_CTR_DRBG' ]

    libdrbg_with_tdea = get_option('with-bc-tdea')
    libdrbg_with_aes = get_option('with-bc-aes')
    if libdrbg_with_tdea
        drbg_build_opts += [ '-DWITH_BC_TDEA' ]
        hash_build_opts += [ '-DWITH_BC_TDEA' ]
    endif

    if libdrbg_with_aes
        drbg_build_opts += [ '-DWITH_BC_AES' ]
    endif
else
    libdrbg_with_tdea = false
    libdrbg_with_aes = false
endif
summary({'CTR DRBG': libdrgb_with_ctr,
         'block cypher TDEA': libdrbg_with_tdea,
         'block cypher AES': libdrbg_with_aes,},
        section: 'libdrgb')

libdrbg_small_footprint = get_option('small-footprint')
if libdrbg_small_footprint
    drbg_build_opts += [ '-DSMALL_MEMORY_FOOTPRINT' ]
endif
summary('small footprint', libdrbg_small_footprint, section: 'libdrgb')

libdrbg_with_test_entropy_source = get_option('with_test_entropy_source')
if libdrbg_with_test_entropy_source
    drbg_build_opts += [ '-DWITH_TEST_ENTROPY_SOURCE' ]
endif
summary('test entropy source', libdrbg_with_test_entropy_source, section: 'libdrgb')

libhash_strict_nist = get_option('strict-nist-hashes')
if libhash_strict_nist
    drbg_build_opts += [ '-DSTRICT_NIST_SP800_90A' ]
    hash_build_opts += [ '-DWITH_HASH_CONF_OVERRIDE' ]
    libhash_enabled_hashes = [
        'sha1',
        'sha224',
        'sha256',
        'sha384',
        'sha512',
        'sha512-224',
        'sha512-256',
    ]
else
    # XXX: remove CONF_OVERRIDE option and generate a config header instead
    libhash_enabled_hashes = get_option('with-hashes')
    if libhash_enabled_hashes.length() != 0
        hash_build_opts += [ '-DWITH_HASH_CONF_OVERRIDE' ]
    endif
endif

summary('strict NIST_SP800_90A', libhash_strict_nist, section: 'libhash')
summary('hashes', libhash_enabled_hashes, section: 'libhash')

foreach hash: libhash_enabled_hashes
    hash_build_opts += '-DWITH_HASH_@0@'.format(hash.underscorify().to_upper())
endforeach

summary({'libdrbg built option': drbg_build_opts,
         'libhash built option': hash_build_opts},
         section: 'build options')

# add_global_arguments([drbg_build_opts, hash_build_opts], language: 'c')

drbg_sources = []

subdir('libhash')

if '-DWITH_BC_AES' in drbg_build_opts
    subdir('aes')
endif

drbg_sources += files([
    'ctr_drbg.c',
    'drbg.c',
    'entropy.c',
    'hash_drbg.c',
    'hmac_drbg.c',
])

drbg_lib = static_library(
    meson.project_name(),
    sources: drbg_sources,
    c_args: drbg_build_opts,
    dependencies: hash_dep
)

drbg_dep = declare_dependency(
    link_with: drbg_lib,
    compile_args: drbg_build_opts,
    dependencies: hash_dep,
)

if get_option('with_test')
    drbg_test = executable(
        meson.project_name(),
        sources: files('main.c',
                       'drbg_tests/ctr_drbg_tests.c',
                       'drbg_tests/hash_drbg_tests.c',
                       'drbg_tests/hmac_drbg_tests.c',),
        include_directories: 'drbg_tests',
        dependencies: drbg_dep,
    )
    test('drbg_self_test', drbg_test)
endif
