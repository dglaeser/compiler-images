# gcc `Dockerfile`

To build this image, simply type

```bash
docker build -t gcc-XX --build-arg TAG=SHA .
```

into your terminal, where `XX` denotes the `gcc` version corresponding to the chosen `SHA` (commit sha or tag).
Check out the [workflow definition](../.github/workflows/build.yml) to see the image that is built by the CI of this repository.
