# Debugging

Rusoto uses the [log][log-repo] logging facade. For tests, Rusoto uses
[`env_logger`][crates-io-env_logger].

In order to see logging output (e.g. the actual requests made to AWS),
`env_logger` needs to be initialized:

```rust,no_run
extern crate rusoto_core;
extern crate rusoto_s3;
extern crate env_logger;

use std::default::Default;

use rusoto_core::Region;
use rusoto_s3::{S3, S3Client, ListObjectsRequest};

fn main() {
    let _ = env_logger::try_init(); // This initializes the `env_logger`

    let bare_s3 = S3Client::new(Region::UsWest2);

    let mut list_request = ListObjectsRequest::default();
    list_request.bucket = "rusototester".to_string();
    let result = bare_s3.list_objects(list_request).sync().unwrap();
    println!("result is {:?}", result);
}
```

To see the output of logging from integration tests, the command needs to be run
as follows:

```
RUST_LOG=rusoto,hyper=debug cargo test --features all
```

To get the logging output as well as the output of any `println!` statements,
run:

```
RUST_LOG=rusoto,hyper=debug cargo test --features all -- --nocapture
```

If more debugging is required, all debug info including details from the compiler can be seen by setting RUST_LOG to `debug`.  This will be noisy but will give a lot of debug information.  For example:

```
RUST_LOG=debug cargo test --features all
```

[crates-io-env_logger]: https://crates.io/crates/env_logger
[log-repo]: https://github.com/rust-lang-nursery/log
