
hash_sources = files([
    'bash.c',
    'bash224.c',
    'bash256.c',
    'bash384.c',
    'bash512.c',
    'belt-hash.c',
    'gostr34_11_94.c',
    'hash.c',
    'hmac.c',
    'md2.c',
    'md4.c',
    'md5.c',
    'mdc2.c',
    'ripemd160.c',
    'sha0.c',
    'sha1.c',
    'sha3-224.c',
    'sha3-256.c',
    'sha3-384.c',
    'sha3-512.c',
    'sha3.c',
    'sha224.c',
    'sha256.c',
    'sha384.c',
    'sha512_core.c',
    'sha512-224.c',
    'sha512-256.c',
    'sha512.c',
    'shake.c',
    'shake256.c',
    'sm3.c',
    'streebog.c',
    'tdes.c',
])

hash_lib = static_library(
    'hash',
    sources: hash_sources,
    c_args: hash_build_opts,
)

hash_dep = declare_dependency(
    link_with: hash_lib,
    compile_args: hash_build_opts,
)
