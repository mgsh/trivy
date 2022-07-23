# SBOM attestation

[Cosign](https://github.com/sigstore/cosign) supports generating and verifying [in-toto attestations](https://github.com/in-toto/attestation). This tool enables you to sign and verify SBOM attestation.

!!! note
    In the following examples, the `cosign` command will write an attestation to a target OCI registry, so you must have permission to write.
    If you want to avoid writing an OCI registry and only want to see an attestation, add the `--no-upload` option to the `cosign` command.

## Sign with a local key pair

Cosign can generate key pairs and use them for signing and verification. Read more about [how to generate key pairs](https://docs.sigstore.dev/cosign/key-generation).

In the following example, Trivy generates an SBOM in the spdx format, and then Cosign attaches an attestation of the SBOM to a container image with a local key pair.

```
$ trivy image --format spdx -o sbom.spdx <IMAGE>
$ cosign attest --key /path/to/cosign.key --type spdx --predicate sbom.spdx <IMAGE>
```

Then, you can verify attestations on the image.

```
$ cosign verify-attestation --key /path/to/cosign.pub <IMAGE>
```

You can also create attestations of other formatted SBOM.

```
# spdx-json
$ trivy image --format spdx-json -o sbom.spdx.json <IMAGE>
$ cosign attest --key /path/to/cosign.key --type spdx --predicate sbom.spdx.json <IMAGE>

# cyclonedx
$ trivy image --format cyclonedx -o sbom.cdx.json <IMAGE>
$ cosign attest --key /path/to/cosign.key --type https://cyclonedx.org/schema --predicate sbom.cdx.json <IMAGE>
```

## Keyless signing

You can use Cosign to sign without keys by authenticating with an OpenID Connect protocol supported by sigstore (Google, GitHub, or Microsoft).

```
$ trivy image --format spdx -o sbom.spdx <IMAGE>
$ COSIGN_EXPERIMENTAL=1 cosign attest --type spdx --predicate sbom.spdx <IMAGE>
```

You can verify attestations.
```
$ COSIGN_EXPERIMENTAL=1 cosign verify-attestation <IMAGE>
```
