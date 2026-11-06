# ADV Java dependency qualification, 2026-11-06

This local branch starts from `fc175dc4443b8fd212ad77d9e8d127b15b41766b`
(the pinned OpenWhisk revision plus the custom OPTIONS authentication fix),
then applies the existing OpenServerless overlay from build commit
`914b30b70fb77c150e5090a0f82e0c5092489219` and this dependency refresh.
The overlay's prior Docker, Gradle, runtime-catalog and etcd changes are retained;
they are not new fixes introduced by this dependency batch.

The new changes are in `build.gradle`, `settings.gradle`,
`common/scala/build.gradle`, `core/invoker/build.gradle` and the Fabric8 test
fixture. Netty, gRPC, Jackson and OkHttp use aligned BOMs. Curator, ZooKeeper,
Fabric8, logging, codecs and utility libraries are updated. The original LZ4
coordinate officially relocates to `at.yawk.lz4`; version 1.11.4 is selected.
Updating the owning AWS SDK removes its obsolete Ion dependency without an
incompatible coordinate substitution. Server configurations select Guava's
standard JVM variant on Gradle 6.

The Fabric8 timeout fixture now keeps a pending pod present throughout the
deadline and returns an EventList. Its timeout assertion remains unchanged;
previously the fixture exhausted its GET responses and returned an unrelated
404 with the updated client.

Controller and invoker distributions were rebuilt from source on Java 11.0.33.
All test sources compiled. The selected existing suites passed 142 tests with
zero failures/skips, both on the original overlay and on this candidate.
The test-only Gradle init script and offline dependency checks are maintained in
`openserverless-build/scripts/advanced-java-dependencies` alongside the image
recipe, source/distribution manifests and qualification record.

On the existing AMD64 ADV lab, 45 selected live checks passed after the invoker
became Healthy: Alpha/Beta workflow, persistence, authentication, streaming,
custom OPTIONS authorization, Python runtime invocations and fixture cleanup.
No baseline VM operation, publication or production qualification is included.
The first early probe received two 503 responses before invoker registration;
that evidence is retained separately from the successful post-readiness batch.

For each rebuilt image, Trivy matches decreased from 5 Critical / 74 High to
0 Critical / 0 High with the same vulnerability databases and no exclusions.
Six Java Medium matches remain in Azure Blob, Akka Management/HTTP and Kafka.
These counts are dependency inventory findings, not demonstrated platform
exploits or a claim that the whole platform is vulnerability-free.

ARM64, a fresh installation, external cloud providers, and the full repository
test suite are not qualified by this batch. The separate old-image CBOR linkage
failure remains recorded; the aligned candidate passes its CBOR round trip.
