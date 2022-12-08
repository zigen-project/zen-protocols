wayland_dtd = join_paths(wayland_scanner_dep.get_variable('pkgdatadir'), 'wayland.dtd')

protocol_sources = []

tests = {}

foreach _, protos : protocols
  foreach _, info : protos
    protocol_sources += info['client-header']
    protocol_sources += info['server-header']
    protocol_sources += info['private-code']
  endforeach
endforeach

tests += {
  'compile': {
    'sources': ['compile.c'] + protocol_sources,
  }
}

foreach test_name, args : tests
  test_sources = [ test_name + '.c', 'test-runner.c' ] + args.get('sources', [])
  test_deps = [ wayland_server_dep ] + args.get('deps', [])
  env = args.get('env', [])
  bin = executable(test_name, test_sources, dependencies: test_deps)
  test(test_name, bin, env: env)
endforeach
