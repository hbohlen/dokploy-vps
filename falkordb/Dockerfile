FROM falkordb/falkordb:edge

RUN mkdir -p /var/lib/falkordb/data && \
    chown -R falkordb:falkordb /var/lib/falkordb

EXPOSE 6379 3000

WORKDIR /var/lib/falkordb

USER falkordb

CMD ["falkordb-server", "--loadmodule", "/usr/lib/falkordb/src/falkordb.so", "--port", "6379", "--bind", "0.0.0.0"]
