conf = configuration_data()
conf.set('prefix', get_option('prefix'))
conf.set('libdir', get_option ('libdir'))
conf.set('PROJECT_NAME', 'valacore')
conf.set('PROJECT_VERSION', '0.0.1')
conf.set('API_VERSION', API_VERSION)

valacore_sources = files([
  'io/Files.vala',
  'io/Path.vala',
  'io/Strings.vala',
  'lang/Os.vala',
  'lang/Objects.vala',
  'parser/ArgParser.vala'
])

libvalacore = library('valacore-' + API_VERSION,
  valacore_sources,
  vala_header: 'valacore.h',
  vala_vapi: 'valacore-' + API_VERSION + '.vapi',
  vala_gir: 'Valacore-' + API_VERSION + '.gir',
  dependencies : dependencies,
  vala_args: [
    '--abi-stability'
    ],
  install: true,
  install_dir: [
    true,
    get_option('includedir') / 'valacore-' + API_VERSION / 'valacore',
    vapidir,
    g_ir_compiler.found()
  ]
)

if g_ir_compiler.found()
  custom_target('libvalacore-typelib',
    command: [
      g_ir_compiler,
      '--shared-library', 'libvalacore-@0@.so'.format (API_VERSION),
      '--output', '@OUTPUT@',
      meson.current_build_dir() / 'Valacore-' + API_VERSION + '.gir'
    ],
    output: 'Valacore-' + API_VERSION + '.typelib',
    depends: libvalacore,
    install: true,
    install_dir: join_paths(get_option('libdir'), 'girepository-1.0'))
endif

pkgconfig.generate(libvalacore,
  subdirs: 'valacore-' + API_VERSION,
  version: API_VERSION,
  name: 'valacore',
  filebase: 'valacore-' + API_VERSION,
  requires: PKGCONFIG_DEPS,
  description: 'libvalacore is basic library for vala language.')

if get_option('enable_valadoc')
  valadoc = find_program('valadoc')
  valacore_docs = custom_target('valacore_apidocs',
    input: valacore_sources,
    install: true,
    install_dir: get_option('datadir') / 'devhelp' / 'books',
    command: [
      valadoc,
      '-o', meson.current_build_dir() / 'Valacore-' + API_VERSION,
      '--doclet',
      'devhelp',
      '--pkg=posix',
      '--pkg=gio-2.0',
      '@INPUT@',
      '--force'
    ],
    output: 'Valacore-' + API_VERSION)

  install_subdir(meson.current_build_dir() / 'Valacore-' + API_VERSION / 'Valacore-' + API_VERSION,
    install_dir: get_option('datadir') / 'devhelp' / 'books')
endif
