cc = meson.get_compiler('c')

nvalhalla_sources = files(
  'cb_redact.c',
  'distancing.gs',
  'nvalhalla.gs',
  'redactor.gs',
  'rtspserversink.gs',
  'utils.gs',
  'validate.gs',
  'youtubesink.gs',
  # 'websinkbin.vala',
)

includes = include_directories('../includes')

# a .pc would be nice in the next release of DeepStream, if you're reading, Nvidia.
ds_includes = include_directories('/opt/nvidia/deepstream/deepstream-5.0/sources/includes')
ds_libdir = '/opt/nvidia/deepstream/deepstream-5.0/lib'

nvalhalla_deps = [
  dependency('gee-0.8'),
  dependency('gio-2.0', version: '>=2.0'),
  dependency('glib-2.0', version: '>=2.50'),
  dependency('gobject-2.0', version: '>=2.0'),
  dependency('gstreamer-1.0', version: '>=1.0'),
  dependency('gstreamer-rtsp-server-1.0', version: '>=1.0'),
  dependency('gstreamer-pbutils-1.0', version: '>=1.0'),
  # this is needed for the SignalHandler:
  meson.get_compiler('vala').find_library('posix'),
  # this is needed for Math
  cc.find_library('m', required : false),
  cc.find_library('nvds_meta', dirs: ds_libdir),
  cc.find_library('nvdsgst_meta', dirs: ds_libdir),
]

# replace the prefix with the actual install prefix
# this isn't perfect (the binary isn't portable after build).
# but it's good enough for now and better than a string const
setup_gs = configure_file(
  input : 'setup.gs',
  output : 'setup.gs',
  configuration : {
  'version': meson.project_version(),
  'prefix': get_option('prefix'),
  }
)
nvalhalla_sources += setup_gs

executable('nvalhalla', 'main.gs', nvalhalla_sources,
  vala_args: [
    '--header=nvalhalla.h',
    '--vapi=nvalhalla.vapi',
  ],
  dependencies: nvalhalla_deps,
  install: true,
  include_directories: [includes, ds_includes],
)
