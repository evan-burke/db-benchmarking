


Benchmark used:
https://github.com/MagicStack/pgbench

### Results here:
https://evan-burke.github.io/db-benchmarking/results.html


## Notes for getting the benchmark up and running: 


I couldn't quite get the Node options working. nodejs/npm install can be skipped if you don't need those.

on ubuntu 16.04:
```shell
sudo apt-get install -y nodejs npm python3-venv postgresql
ln -s /usr/bin/nodejs /usr/bin/node
wget https://bootstrap.pypa.io/get-pip.py && python3 get-pip.py

# pull latest Go. You can always get it from golang.org/dl/ if this doesn't work.
wget https://redirector.gvt1.com/edgedl/go/go1.9.2.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.9.2.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin

python3 -m venv pgbench-venv
source pgbench-venv/bin/activate

git clone https://github.com/MagicStack/pgbench.git
cd pgbench
pip install -r requirements.txt
```

The automatic pg cluster creation in the benchmark script wasn't working for me, so I set up a local postgres user for this.

Edit this:
```shell
sudo nano /etc/postgresql/*/main/pg_hba.conf
```

Set both of these lines  to 'trust' instead of 'peer' and 'md5':
```
# Database administrative login by Unix domain socket
local   all             postgres                                peer
...
host    all             all             127.0.0.1/32            md5
```

Reload changes & set up benchmark using make:
```shell
sudo service postgresql reload
make
```

If skipping node, edit the pgbench file to remove node benchmarks from the following list, around line 360:
```
BENCHMARKS = [ ... 
```

Run bench. Takes ~20 min or so with these settings.
```shell
./pgbench --pguser postgres --pghost 127.0.0.1 -H results.html -J results.json
```



## Todo: 
Maybe add these to the benchmark script and compare against it? 
SqlAlchemy wrapper for asyncpg; their brief tests show it's slightly faster in some cases (how?)
https://github.com/CanopyTax/asyncpgsa

turodbc - oriented towards batched operations, not single-row. support for exasol, Arrow, numpy, in addition to standard relational DBs.
https://github.com/blue-yonder/turbodbc
