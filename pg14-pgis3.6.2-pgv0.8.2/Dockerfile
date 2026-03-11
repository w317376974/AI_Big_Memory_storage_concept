FROM postgres:14-bullseye
# 更换国内源（可选）
RUN sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list

# 安装编译工具、PostGIS 依赖和 PostgreSQL 开发包
RUN apt-get update && apt-get remove --purge libgeos-dev libgeos-c1v5 && apt-get install -y --no-install-recommends \
        build-essential \
        libgeos-dev \
        libproj-dev \
        libgdal-dev \
        libxml2-dev \
        git \
        cmake \
        libjson-c-dev \
        libprotobuf-c-dev \
        protobuf-c-compiler \
        ca-certificates \
        wget \
        postgresql-server-dev-14 \
    && rm -rf /var/lib/apt/lists/*

# 复制源码包
WORKDIR /usr/src
RUN  wget -O /usr/src/geos-3.14.1.tar.bz2 https://download.osgeo.org/geos/geos-3.14.1.tar.bz2
RUN  wget -O /usr/src/pgvector-0.8.2.tar.gz https://codeload.github.com/pgvector/pgvector/tar.gz/refs/tags/v0.8.2
RUN  wget -O /usr/src/postgis-3.6.2.tar.gz https://download.osgeo.org/postgis/source/postgis-3.6.2.tar.gz

RUN tar -xvjf geos-3.14.1.tar.bz2 && \
    cd geos-3.14.1 && \
    mkdir build && \
    cd build && \
    cmake ..&& \
    make -j $(nproc) && \
    make install && \
    ldconfig && \
    cd ../.. && \
    rm -rf geos-3.14.1


# 编译安装 PostGIS
RUN tar xzf postgis-3.6.2.tar.gz && \
    cd postgis-3.6.2 && \
    ./configure --with-pgconfig=/usr/lib/postgresql/14/bin/pg_config && \
    make -j $(nproc) && \
    make install && \
    cd .. && \
    rm -rf postgis-3.6.2

# 编译安装 pgvector
RUN tar xzf pgvector-0.8.2.tar.gz && \
    cd pgvector-0.8.2 && \
    make -j $(nproc) && \
    make install && \
    cd .. && \
    rm -rf pgvector-0.8.2

# 可选：预加载 pgvector 库
RUN echo "shared_preload_libraries = 'vector'" >> /usr/share/postgresql/postgresql.conf.sample
