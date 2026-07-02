---
project: sfpi-gcc
github: https://github.com/tenstorrent/sfpi-gcc
deepwiki: https://deepwiki.com/tenstorrent/sfpi-gcc/4-language-support
---

# Language Support

Relevant source files
*   [gcc/ada/aspects.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/aspects.ads)
*   [gcc/ada/checks.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/checks.adb)
*   [gcc/ada/contracts.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/contracts.adb)
*   [gcc/ada/doc/gnat_rm/implementation_defined_aspects.rst](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/doc/gnat_rm/implementation_defined_aspects.rst)
*   [gcc/ada/einfo.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/einfo.ads)
*   [gcc/ada/exp_aggr.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_aggr.adb)
*   [gcc/ada/exp_attr.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_attr.adb)
*   [gcc/ada/exp_ch13.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch13.ads)
*   [gcc/ada/exp_ch3.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch3.adb)
*   [gcc/ada/exp_ch3.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch3.ads)
*   [gcc/ada/exp_ch4.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch4.adb)
*   [gcc/ada/exp_ch6.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch6.adb)
*   [gcc/ada/exp_ch7.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch7.adb)
*   [gcc/ada/exp_ch9.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch9.adb)
*   [gcc/ada/exp_disp.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_disp.adb)
*   [gcc/ada/exp_prag.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_prag.adb)
*   [gcc/ada/exp_spark.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_spark.adb)
*   [gcc/ada/exp_util.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_util.adb)
*   [gcc/ada/fmap.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/fmap.ads)
*   [gcc/ada/freeze.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/freeze.adb)
*   [gcc/ada/gen_il-fields.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/gen_il-fields.ads)
*   [gcc/ada/gen_il-gen-gen_entities.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/gen_il-gen-gen_entities.adb)
*   [gcc/ada/gen_il-gen-gen_nodes.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/gen_il-gen-gen_nodes.adb)
*   [gcc/ada/gen_il-types.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/gen_il-types.ads)
*   [gcc/ada/gnat_cuda.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/gnat_cuda.adb)
*   [gcc/ada/gnat_cuda.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/gnat_cuda.ads)
*   [gcc/ada/libgnat/a-iteint.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/libgnat/a-iteint.ads)
*   [gcc/ada/par-ch4.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/par-ch4.adb)
*   [gcc/ada/par-prag.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/par-prag.adb)
*   [gcc/ada/prep.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/prep.adb)
*   [gcc/ada/sem_aggr.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_aggr.adb)
*   [gcc/ada/sem_attr.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_attr.adb)
*   [gcc/ada/sem_cat.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_cat.adb)
*   [gcc/ada/sem_ch10.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch10.adb)
*   [gcc/ada/sem_ch12.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch12.adb)
*   [gcc/ada/sem_ch13.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch13.adb)
*   [gcc/ada/sem_ch3.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch3.adb)
*   [gcc/ada/sem_ch4.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch4.adb)
*   [gcc/ada/sem_ch5.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch5.adb)
*   [gcc/ada/sem_ch6.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch6.adb)
*   [gcc/ada/sem_ch8.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch8.adb)
*   [gcc/ada/sem_ch9.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch9.adb)
*   [gcc/ada/sem_elim.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_elim.adb)
*   [gcc/ada/sem_eval.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_eval.adb)
*   [gcc/ada/sem_eval.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_eval.ads)
*   [gcc/ada/sem_prag.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_prag.adb)
*   [gcc/ada/sem_prag.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_prag.ads)
*   [gcc/ada/sem_res.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_res.adb)
*   [gcc/ada/sem_res.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_res.ads)
*   [gcc/ada/sem_type.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_type.adb)
*   [gcc/ada/sem_type.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_type.ads)
*   [gcc/ada/sem_util.adb](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_util.adb)
*   [gcc/ada/sem_util.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_util.ads)
*   [gcc/ada/sinfo.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sinfo.ads)
*   [gcc/ada/snames.ads-tmpl](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/snames.ads-tmpl)
*   [gcc/ada/stringt.ads](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/stringt.ads)
*   [gcc/c-family/c-common.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c-family/c-common.h)
*   [gcc/c-family/c-pragma.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c-family/c-pragma.h)
*   [gcc/config/rs6000/aix.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/aix.h)
*   [gcc/config/rs6000/aix64.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/aix64.opt)
*   [gcc/config/rs6000/aix71.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/aix71.h)
*   [gcc/config/rs6000/aix72.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/aix72.h)
*   [gcc/config/rs6000/aix73.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/aix73.h)
*   [gcc/config/rs6000/altivec.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/altivec.h)
*   [gcc/config/rs6000/darwin.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/darwin.h)
*   [gcc/config/rs6000/option-defaults.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/option-defaults.h)
*   [gcc/config/rs6000/rbtree.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rbtree.h)
*   [gcc/config/rs6000/rs6000-builtin.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-builtin.cc)
*   [gcc/config/rs6000/rs6000-builtins.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-builtins.def)
*   [gcc/config/rs6000/rs6000-c.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-c.cc)
*   [gcc/config/rs6000/rs6000-gen-builtins.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-gen-builtins.cc)
*   [gcc/config/rs6000/rs6000-internal.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-internal.h)
*   [gcc/config/rs6000/rs6000-overload.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-overload.def)
*   [gcc/config/rs6000/rs6000.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000.h)
*   [gcc/config/rs6000/rtems.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rtems.h)
*   [gcc/config/rs6000/t-rs6000](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/t-rs6000)
*   [gcc/cp/call.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/call.cc)
*   [gcc/cp/constraint.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/constraint.cc)
*   [gcc/cp/cp-tree.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/cp-tree.h)
*   [gcc/cp/parser.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/parser.h)
*   [gcc/cp/pt.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/pt.cc)
*   [gcc/doc/extend.texi](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/extend.texi)
*   [gcc/testsuite/c-c++-common/gomp/clauses-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/c-c++-common/gomp/clauses-1.c)
*   [gcc/testsuite/g++.dg/cpp0x/alias-decl-dr1286a.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/alias-decl-dr1286a.C)
*   [gcc/testsuite/g++.dg/cpp0x/inh-ctor37.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/inh-ctor37.C)
*   [gcc/testsuite/g++.dg/cpp0x/initlist122.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/initlist122.C)
*   [gcc/testsuite/g++.dg/cpp0x/initlist129.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/initlist129.C)
*   [gcc/testsuite/g++.dg/cpp0x/lambda/lambda-nested9.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/lambda/lambda-nested9.C)
*   [gcc/testsuite/g++.dg/cpp0x/lambda/lambda-nested9a.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/lambda/lambda-nested9a.C)
*   [gcc/testsuite/g++.dg/cpp0x/lambda/lambda-switch.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/lambda/lambda-switch.C)
*   [gcc/testsuite/g++.dg/cpp0x/nsdmi17.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/nsdmi17.C)
*   [gcc/testsuite/g++.dg/cpp0x/ref-bind4.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/ref-bind4.C)
*   [gcc/testsuite/g++.dg/cpp0x/ref-bind8.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp0x/ref-bind8.C)
*   [gcc/testsuite/g++.dg/cpp1y/auto-fn63.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp1y/auto-fn63.C)
*   [gcc/testsuite/g++.dg/cpp1y/lambda-generic-95451.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp1y/lambda-generic-95451.C)
*   [gcc/testsuite/g++.dg/cpp1z/aggr-base12.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp1z/aggr-base12.C)
*   [gcc/testsuite/g++.dg/cpp1z/class-deduction-alias1.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp1z/class-deduction-alias1.C)
*   [gcc/testsuite/g++.dg/cpp2a/concepts-memtmpl6.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp2a/concepts-memtmpl6.C)
*   [gcc/testsuite/g++.dg/cpp2a/consteval26.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp2a/consteval26.C)
*   [gcc/testsuite/g++.dg/cpp2a/consteval31.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp2a/consteval31.C)
*   [gcc/testsuite/g++.dg/cpp2a/constinit16.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp2a/constinit16.C)
*   [gcc/testsuite/g++.dg/cpp2a/destroying-delete5.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp2a/destroying-delete5.C)
*   [gcc/testsuite/g++.dg/cpp2a/destroying-delete6.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp2a/destroying-delete6.C)
*   [gcc/testsuite/g++.dg/cpp2a/lambda-pack-init6.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp2a/lambda-pack-init6.C)
*   [gcc/testsuite/g++.dg/cpp2a/spaceship-virtual1.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/cpp2a/spaceship-virtual1.C)
*   [gcc/testsuite/g++.dg/ext/conv2.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/ext/conv2.C)
*   [gcc/testsuite/g++.dg/ext/is_trivially_constructible7.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/ext/is_trivially_constructible7.C)
*   [gcc/testsuite/g++.dg/gomp/attrs-1.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/gomp/attrs-1.C)
*   [gcc/testsuite/g++.dg/gomp/attrs-10.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/gomp/attrs-10.C)
*   [gcc/testsuite/g++.dg/gomp/attrs-11.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/gomp/attrs-11.C)
*   [gcc/testsuite/g++.dg/gomp/attrs-2.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/gomp/attrs-2.C)
*   [gcc/testsuite/g++.dg/gomp/attrs-8.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/gomp/attrs-8.C)
*   [gcc/testsuite/g++.dg/init/aggr7-eh.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/init/aggr7-eh.C)
*   [gcc/testsuite/g++.dg/init/bitfield6.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/init/bitfield6.C)
*   [gcc/testsuite/g++.dg/lookup/new3.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/lookup/new3.C)
*   [gcc/testsuite/g++.dg/template/conv17.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/template/conv17.C)
*   [gcc/testsuite/g++.dg/template/non-dependent24.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/template/non-dependent24.C)
*   [gcc/testsuite/g++.dg/template/redecl5.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/template/redecl5.C)
*   [gcc/testsuite/g++.dg/tree-ssa/aggregate1.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/tree-ssa/aggregate1.C)
*   [gcc/testsuite/g++.dg/warn/Wclass-memaccess-7.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/warn/Wclass-memaccess-7.C)
*   [gcc/testsuite/g++.dg/warn/Wreturn-type-13.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/warn/Wreturn-type-13.C)
*   [gcc/testsuite/gcc.dg/pr99708.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/pr99708.c)
*   [gcc/testsuite/gcc.dg/sso-14.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/sso-14.c)
*   [gcc/testsuite/gcc.target/powerpc/bfp/scalar-extract-sig-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/bfp/scalar-extract-sig-2.c)
*   [gcc/testsuite/gcc.target/powerpc/bfp/scalar-insert-exp-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/bfp/scalar-insert-exp-2.c)
*   [gcc/testsuite/gcc.target/powerpc/bfp/scalar-insert-exp-5.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/bfp/scalar-insert-exp-5.c)
*   [gcc/testsuite/gcc.target/powerpc/bfp/scalar-insert-exp-8.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/bfp/scalar-insert-exp-8.c)
*   [gcc/testsuite/gcc.target/powerpc/bfp/scalar-test-neg-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/bfp/scalar-test-neg-2.c)
*   [gcc/testsuite/gcc.target/powerpc/bfp/scalar-test-neg-3.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/bfp/scalar-test-neg-3.c)
*   [gcc/testsuite/gcc.target/powerpc/builtins-4.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/builtins-4.c)
*   [gcc/testsuite/gcc.target/powerpc/convert-fp-128.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/convert-fp-128.c)
*   [gcc/testsuite/gcc.target/powerpc/mod-vectorize.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/mod-vectorize.c)
*   [gcc/testsuite/gcc.target/powerpc/mul-vectorize-3.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/mul-vectorize-3.c)
*   [gcc/testsuite/gcc.target/powerpc/mul-vectorize-4.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/mul-vectorize-4.c)
*   [gcc/testsuite/gcc.target/powerpc/not-promote-mode.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/not-promote-mode.c)
*   [gcc/testsuite/gcc.target/powerpc/p10_vec_xl_sext.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/p10_vec_xl_sext.c)
*   [gcc/testsuite/gcc.target/powerpc/p9-sign_extend-runnable.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/p9-sign_extend-runnable.c)
*   [gcc/testsuite/gcc.target/powerpc/pr101985-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr101985-1.c)
*   [gcc/testsuite/gcc.target/powerpc/pr101985-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr101985-2.c)
*   [gcc/testsuite/gcc.target/powerpc/pr105271.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr105271.c)
*   [gcc/testsuite/gcc.target/powerpc/pr99492.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr99492.c)
*   [gcc/testsuite/gcc.target/powerpc/pr99557.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr99557.c)
*   [gcc/testsuite/gcc.target/powerpc/pr99708-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr99708-2.c)
*   [gcc/testsuite/gcc.target/powerpc/pr99708.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr99708.c)
*   [gcc/testsuite/gcc.target/powerpc/vec-msumc.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/vec-msumc.c)
*   [gcc/testsuite/obj-c++.dg/pr49070.mm](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/obj-c++.dg/pr49070.mm)
*   [gcc/testsuite/objc.dg/unnamed-parms.m](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/objc.dg/unnamed-parms.m)
*   [gcc/tree.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree.h)

This page documents GCC's multi-language compiler architecture, which supports C, C++, Ada, Fortran, D, and other programming languages through a unified compilation infrastructure. Each language has a dedicated frontend that parses source code and produces a common intermediate representation (GIMPLE) for optimization and code generation.

For detailed information about specific language features:

*   C++ template processing, constraint checking, and concept satisfaction: see [C++ Template System](https://deepwiki.com/tenstorrent/sfpi-gcc/4.1-c++-template-system)
*   Ada semantic analysis, type system, and expansion: see [Ada Compiler (GNAT)](https://deepwiki.com/tenstorrent/sfpi-gcc/4.2-ada-compiler-(gnat))
*   GCC-specific language extensions to C/C++: see [GCC Language Extensions](https://deepwiki.com/tenstorrent/sfpi-gcc/4.3-gcc-language-extensions)

## Multi-Language Frontend Architecture

GCC's language support is organized as a collection of language-specific frontends that share common middle-end infrastructure. Each frontend is responsible for parsing its language and generating GIMPLE intermediate representation.

**Frontend Architecture Overview**

Sources: [gcc/tree.h 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree.h#L1-L100) Diagram 1 in high-level architecture

## C++ Frontend

The C++ frontend implements the full C++ standard including templates, concepts, constraints, and overload resolution. Key components include:

### Parser and Type System

The C++ parser (`cp/parser.cc`) converts source code to an abstract syntax tree (AST) represented using tree nodes defined in `cp/cp-tree.h`. The parser handles:

*   Template declarations and instantiation
*   Class and namespace definitions
*   Expression parsing with overload resolution
*   Modern C++ features (lambdas, concepts, coroutines)

### Template Processing

Template instantiation is handled by `cp/pt.cc`, which manages:

*   **Template parameter substitution**: The `tsubst` family of functions
*   **Instantiation context**: `tinst_level` tracks instantiation depth
*   **Specialization management**: `spec_hasher` and `decl_specializations` table
*   **Deferred instantiation**: `pending_template` list

Key data structures:

*   `TEMPLATE_INFO`: Stores template declaration and arguments ([gcc/cp/cp-tree.h 583-589](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/cp-tree.h#L583-L589))
*   `TEMPLATE_PARM_INDEX`: Template parameter representation
*   `canonical_template_parms`: Canonical parameter types ([gcc/cp/pt.cc 123-127](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/pt.cc#L123-L127))

### Constraint Checking

C++20 concepts and constraints are processed by `cp/constraint.cc`:

*   **Constraint normalization**: Converting requires-expressions to logical formulas
*   **Satisfaction checking**: Determining if constraints are met
*   **Subsumption**: Determining constraint relationships
*   **Diagnostic improvements**: Better error messages for constraint failures

**C++ Frontend Pipeline**

Sources: [gcc/cp/pt.cc 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/pt.cc#L1-L100)[gcc/cp/constraint.cc 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/constraint.cc#L1-L50)[gcc/cp/call.cc 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/call.cc#L1-L50)[gcc/cp/cp-tree.h 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/cp-tree.h#L1-L100)

### Type System

The C++ type system extends GCC's base type representation with language-specific features:

**C++ Type Hierarchy**

Sources: [gcc/cp/cp-tree.h 400-650](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/cp-tree.h#L400-L650)[gcc/tree.h 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree.h#L1-L100)

## Ada Frontend (GNAT)

The Ada frontend (GNAT - GNU NYU Ada Translator) is a complete Ada compiler integrated into GCC. It consists of semantic analysis passes (`sem_*.adb`) and expansion/lowering passes (`exp_*.adb`).

### Semantic Analysis Architecture

Ada semantic analysis is organized into multiple specialized packages:

| Package | Purpose | Key Functions |
| --- | --- | --- |
| `sem_ch3.adb` | Type declarations and derivations | `Build_Derived_Type`, `Analyze_Type_Declaration` |
| `sem_ch4.adb` | Expression analysis | `Analyze_Expression`, operator resolution |
| `sem_ch6.adb` | Subprogram declarations | Parameter conformance, overloading |
| `sem_ch8.adb` | Visibility and name resolution | `Find_Direct_Name`, scope management |
| `sem_ch12.adb` | Generic instantiation | Template-like generic processing |
| `sem_ch13.adb` | Representation clauses | Aspects, attributes, pragmas |
| `sem_util.adb` | Semantic utilities | Type checking, constraint checks |
| `sem_res.adb` | Resolution and overloading | `Resolve`, type disambiguation |
| `sem_type.adb` | Type operations | Type compatibility, conversions |
| `sem_aggr.adb` | Aggregate expressions | Array and record aggregates |
| `sem_attr.adb` | Attribute handling | Built-in attributes like 'Length, 'First |
| `sem_prag.adb` | Pragma processing | Compiler directives |
| `sem_eval.adb` | Static expression evaluation | Compile-time constant folding |

**Ada Semantic Analysis Pipeline**

Sources: [gcc/ada/sem_ch3.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch3.adb#L1-L100)[gcc/ada/sem_ch4.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch4.adb#L1-L100)[gcc/ada/sem_ch6.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch6.adb#L1-L100)[gcc/ada/sem_util.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_util.adb#L1-L100)[gcc/ada/sem_res.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_res.adb#L1-L100)

### Entity Information System

Ada uses a sophisticated entity information system defined in `einfo.ads` and generated by `gen_il-gen-gen_entities.adb`. Entities represent all program elements (types, variables, subprograms, packages, etc.) with their semantic properties.

Key entity kinds:

*   **Type entities**: `E_Array_Type`, `E_Record_Type`, `E_Access_Type`, `E_Enumeration_Type`
*   **Object entities**: `E_Variable`, `E_Constant`, `E_Component`, `E_Discriminant`
*   **Subprogram entities**: `E_Function`, `E_Procedure`, `E_Operator`
*   **Package entities**: `E_Package`, `E_Generic_Package`

Entity attributes (examples from [gcc/ada/einfo.ads 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/einfo.ads#L1-L100)):

*   Type properties: `Is_Tagged_Type`, `Has_Discriminants`, `Is_Abstract_Type`
*   Object properties: `Ekind`, `Etype`, `Scope`, `Is_Aliased`
*   Subprogram properties: `First_Entity`, `Last_Entity`, `Overridden_Operation`

### Expansion and Code Generation

Expansion transforms high-level Ada constructs into lower-level representations:

| Package | Purpose | Transformations |
| --- | --- | --- |
| `exp_ch3.adb` | Type expansion | Initialize_Scalars, Build_Init_Procedure |
| `exp_ch4.adb` | Expression expansion | Short-circuit operators, membership tests |
| `exp_ch6.adb` | Subprogram expansion | Parameter passing, in-out mode handling |
| `exp_ch7.adb` | Finalization | Controlled types, adjust/finalize calls |
| `exp_aggr.adb` | Aggregate expansion | Array/record aggregate flattening |
| `exp_attr.adb` | Attribute expansion | Runtime calls for dynamic attributes |
| `exp_disp.adb` | Dispatching | Tagged type dispatch tables |
| `exp_util.adb` | Expansion utilities | Name generation, side effect removal |

**Ada Expansion Pipeline**

Sources: [gcc/ada/exp_ch3.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch3.adb#L1-L100)[gcc/ada/exp_ch4.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch4.adb#L1-L100)[gcc/ada/exp_ch6.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch6.adb#L1-L100)[gcc/ada/exp_ch7.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch7.adb#L1-L100)[gcc/ada/exp_aggr.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_aggr.adb#L1-L100)[gcc/ada/exp_util.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_util.adb#L1-L100)

### Constraint and Validity Checking

Ada requires extensive runtime checking. The `checks.adb` package implements:

*   **Discriminant checks**: Verify discriminant constraints on variant records
*   **Range checks**: Ensure scalar values are within subtype bounds
*   **Overflow checks**: Detect arithmetic overflow
*   **Access checks**: Null pointer checks
*   **Elaboration checks**: Ensure proper initialization order
*   **Validity checks**: Verify representation validity

These checks are inserted during semantic analysis and expansion, controlled by compiler switches and pragmas like `pragma Suppress` and `pragma Unsuppress`.

Sources: [gcc/ada/checks.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/checks.adb#L1-L100)

### Generic Instantiation

Ada generics (similar to C++ templates) are handled by `sem_ch12.adb`. The instantiation process:

1.   **Copy generic body**: Create a copy of the generic unit's declarations
2.   **Substitute actuals**: Replace formal parameters with actual arguments
3.   **Reanalyze**: Perform full semantic analysis on the instance
4.   **Generate code**: Expand the instantiated unit

Unlike C++ templates, Ada generic instantiation happens during semantic analysis, not on-demand during code generation.

Sources: [gcc/ada/sem_ch12.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_ch12.adb#L1-L100)

## Language-Independent Type System

All GCC frontends use a common tree-based type representation defined in `tree.h` and `tree-core.h`. Language frontends extend this with language-specific attributes.

### Core Type Nodes

**Common Type Hierarchy**

Sources: [gcc/tree.h 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree.h#L1-L100)

### Type Attributes

Types carry language-specific attributes through:

*   **Type qualifiers**: `const`, `volatile`, `restrict` encoded in `TYPE_QUALS`
*   **Alignment**: `TYPE_ALIGN` specifies type alignment in bits
*   **Size**: `TYPE_SIZE` and `TYPE_SIZE_UNIT` for compile-time sizes
*   **Language flags**: `TREE_LANG_FLAG_*` for language-specific properties

## Built-in Functions and Intrinsics

GCC provides built-in functions that map directly to target instructions or runtime library calls. These are available across multiple languages.

### PowerPC Built-in Architecture

PowerPC has an extensive built-in function system with overloading support:

**PowerPC Built-in Function Resolution**

The `rs6000-overload.def` file defines overloaded function signatures like:

```
[VEC_ADD, vec_add, __builtin_vec_add]
  vsc __builtin_vec_add (vsc, vsc);
  vuc __builtin_vec_add (vuc, vuc);
  vss __builtin_vec_add (vss, vss);
  vus __builtin_vec_add (vus, vus);
  ...
```

Each overload resolves to a specific builtin in `rs6000-builtins.def`:

```
[VEC_ADDUDM, vec_addudm, __builtin_vsx_addudm]
  vsll __builtin_vsx_addudm (vsll, vsll);
```

Sources: [gcc/config/rs6000/rs6000-overload.def 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-overload.def#L1-L100)[gcc/config/rs6000/rs6000-builtins.def 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000-builtins.def#L1-L100)[gcc/config/rs6000/rs6000.h 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/rs6000/rs6000.h#L1-L100)

### Cross-Language Built-in Functions

Common built-ins available in C, C++, and other languages:

| Category | Examples | Purpose |
| --- | --- | --- |
| Arithmetic | `__builtin_add_overflow`, `__builtin_mul_overflow` | Overflow detection |
| Memory | `__builtin_memcpy`, `__builtin_memset` | Memory operations |
| Bit manipulation | `__builtin_popcount`, `__builtin_clz` | Bit operations |
| Atomic | `__atomic_load`, `__atomic_store` | Lock-free atomics |
| Math | `__builtin_sqrt`, `__builtin_sin` | Math functions |
| Object size | `__builtin_object_size` | Security checks |
| Frame address | `__builtin_frame_address` | Stack introspection |

Built-in function codes are defined in `tree-core.h` as `enum built_in_function`.

Sources: [gcc/tree.h 20-60](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree.h#L20-L60)[gcc/doc/extend.texi 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/extend.texi#L1-L50)

## Language Extensions

GCC provides numerous extensions to standard C and C++ that are documented in detail on the [GCC Language Extensions](https://deepwiki.com/tenstorrent/sfpi-gcc/4.3-gcc-language-extensions) page. Key extension categories include:

### Additional Numeric Types

Extended integer and floating-point types beyond standard language types:

| Extension | Description | Availability |
| --- | --- | --- |
| `__int128` | 128-bit integer type | x86_64, PowerPC, IA-64, LoongArch |
| `__float128` | 128-bit floating point | x86_64, PowerPC, IA-64 |
| `__float80` | 80-bit extended precision | x86, x86_64, IA-64 |
| `__ibm128` | IBM extended double | PowerPC |
| `_Float16` | 16-bit half precision | AArch64, ARM, x86 with SSE2 |
| `_Float32`, `_Float64` | ISO/IEC TS 18661-3 types | Architecture-dependent |

Example usage from [gcc/doc/extend.texi 59-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/extend.texi#L59-L100):

### Complex Number Support

GCC extends C90 and C++ with complex number types using `_Complex` keyword:

The `__builtin_complex` function constructs complex values with proper handling of infinities and NaNs:

Sources: [gcc/doc/extend.texi 102-210](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/extend.texi#L102-L210)

### Aggregate Type Extensions

Extensions to array, structure, and union types:

*   **Named address spaces**: Target-specific address space qualifiers
*   **Variable-length arrays**: Arrays with runtime-determined size (C99 feature)
*   **Zero-length arrays**: `int arr[0]` as struct member
*   **Flexible array members**: C99 feature `int arr[]` at end of struct
*   **Case ranges**: `case 1 ... 5:` in switch statements
*   **Designated initializers**: `.field = value` in struct initializers

Sources: [gcc/doc/extend.texi 26-39](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/extend.texi#L26-L39)

## Pragma and Attribute Processing

Both C/C++ and Ada support pragmas (Ada) / attributes (GCC) to control compilation:

### Ada Pragma System

Ada pragmas are processed by `sem_prag.adb` which handles:

*   **Representation pragmas**: `Pack`, `Volatile`, `Atomic`, `Address`
*   **Compilation pragmas**: `Inline`, `Pure`, `Preelaborate`
*   **Assertion pragmas**: `Assert`, `Precondition`, `Postcondition`
*   **Implementation pragmas**: `Import`, `Export`, `Convention`
*   **Warning control**: `Warnings (Off/On)`

The pragma processing pipeline:

1.   Parser recognizes pragma syntax
2.   `sem_prag.adb` validates pragma arguments
3.   Pragma effects recorded in entity attributes (via `einfo.ads`)
4.   Expansion and code generation respect pragma semantics

Sources: [gcc/ada/sem_prag.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_prag.adb#L1-L100)

### GCC Attribute System

C/C++ use `__attribute__((...))` syntax processed by language frontends. Attributes can apply to:

*   Types: `__attribute__((packed))`, `__attribute__((aligned(16)))`
*   Variables: `__attribute__((section(".data")))`, `__attribute__((weak))`
*   Functions: `__attribute__((noreturn))`, `__attribute__((always_inline))`

Attributes are stored in tree nodes and queried during optimization and code generation.

Sources: [gcc/doc/extend.texi 29](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/extend.texi#L29-L29)[gcc/tree.h 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree.h#L1-L100)

## Semantic Name System

All GCC frontends use a name table system for efficient string handling. Ada's system is particularly well-developed:

### Ada Name System (snames.ads)

The `snames.ads` template file defines:

*   **Predefined names**: `Name_Integer`, `Name_Boolean`, `Name_Standard`
*   **Attribute names**: `Name_Address`, `Name_Size`, `Name_Range`
*   **Pragma names**: `Name_Inline`, `Name_Pack`, `Name_Import`
*   **Convention names**: `Name_Ada`, `Name_C`, `Name_Fortran`

Name table functions:

*   `Get_Name_String`: Retrieve string from name ID
*   `String_To_Name`: Convert string to name ID
*   `Name_Find`: Look up or create name entry

This system provides O(1) name comparison by comparing integer IDs instead of strings.

Sources: [gcc/ada/snames.ads-tmpl 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/snames.ads-tmpl#L1-L100)

## Freeze and Elaboration

Ada has unique requirements for type "freezing" - the point where type layout is finalized:

### Freeze Mechanism

The `freeze.adb` package handles:

1.   **Type freezing**: Finalize type representation, generate initialization procedures
2.   **Delayed aspects**: Process aspects that depend on complete type information
3.   **Implicit operations**: Generate = operator, stream procedures, etc.
4.   **Representation clause validation**: Verify layout meets requirements

A type is frozen when:

*   It is used to declare an object
*   It appears in an allocator
*   It is used in a primitive operation declaration
*   The end of its declarative part is reached

**Ada Type Freezing Process**

Sources: [gcc/ada/freeze.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/freeze.adb#L1-L100)[gcc/ada/exp_ch3.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/exp_ch3.adb#L1-L100)

### Elaboration Order

Ada requires careful elaboration order determination to avoid accessing uninitialized data. This is more complex than C/C++ static initialization and is handled specially by the Ada frontend.

Sources: [gcc/ada/sem_util.adb 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/sem_util.adb#L1-L100)

## Language Frontend Comparison

Summary comparison of frontend architectures:

| Aspect | C++ | Ada (GNAT) | C | Fortran |
| --- | --- | --- | --- | --- |
| **Parser location** | `cp/parser.cc` | `ada/par-*.adb` | `c/c-parser.c` | `fortran/parse.cc` |
| **Template/Generic** | `cp/pt.cc` (on-demand) | `sem_ch12.adb` (immediate) | N/A | N/A |
| **Type system** | `cp/cp-tree.h` | `einfo.ads` | `c-tree.h` | `fortran/gfortran.h` |
| **Overloading** | `cp/call.cc` complex | `sem_res.adb` moderate | Limited | Limited |
| **Constraint checking** | `cp/constraint.cc` (concepts) | `checks.adb` (runtime) | Minimal | Array bounds |
| **Code expansion** | Limited | Extensive (`exp_*.adb`) | Minimal | Moderate |
| **Representation control** | Limited | Extensive (aspects/pragmas) | Limited | Limited |

Sources: Multiple files from table of contents and architecture diagrams

Dismiss
Refresh this wiki

Enter email to refresh