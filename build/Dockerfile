FROM openscad/wasm-base-release@sha256:f73d33d5f2fd4c7ae4d3aaacb1e2e2deb193b878b38bb80c8235c933ac340c66 AS build

ARG OPENSCAD_COMMIT=0a66508c67374febcfc814a73b5b948dd84a1ca3
ARG EMSCRIPTEN_FLAGS="-fexceptions -O3"
ENV CMAKE_BUILD_TYPE=Release \
    CMAKE_C_FLAGS="${EMSCRIPTEN_FLAGS}" \
    CMAKE_CXX_FLAGS="${EMSCRIPTEN_FLAGS}" \
    CMAKE_EXE_LINKER_FLAGS="${EMSCRIPTEN_FLAGS}"

WORKDIR /home/ubuntu/openscad
RUN git init -q . && \
    git remote add origin https://github.com/openscad/openscad.git && \
    git fetch --depth 1 origin "${OPENSCAD_COMMIT}" && \
    git checkout -q FETCH_HEAD && \
    git submodule update --init --recursive --depth 1

RUN emcmake cmake -B /home/ubuntu/build . \
        -DBoost_USE_STATIC_RUNTIME=ON \
        -DBoost_USE_STATIC_LIBS=ON \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_BUILD_PARALLEL_LEVEL=4 \
        -DEXPERIMENTAL=ON \
        -DSNAPSHOT=ON \
        -G Ninja && \
    cmake --build /home/ubuntu/build --parallel

RUN [ "$(head -c4 /home/ubuntu/build/openscad.wasm | od -An -tx1 | tr -d ' \n')" = "0061736d" ] && \
    [ "$(stat -c%s /home/ubuntu/build/openscad.wasm)" -gt 1048576 ] && \
    grep -q "export default" /home/ubuntu/build/openscad.js && \
    mkdir -p /dist && \
    cp /home/ubuntu/build/openscad.js  /dist/openscad.mjs && \
    cp /home/ubuntu/build/openscad.wasm /dist/openscad.wasm && \
    (cp /home/ubuntu/build/openscad.wasm.map /dist/ 2>/dev/null || true) && \
    printf 'openscad@%s\n' "${OPENSCAD_COMMIT}" > /dist/OPENSCAD_VERSION && \
    ls -lh /dist

FROM scratch AS exportor
COPY --from=build /dist /dist
