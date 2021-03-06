# Parity JSON-RPC

Rust implementation of JSON-RPC 2.0 Specification.
Transport-agnostic `core` and transport servers for `http`, `ipc`, `websockets` and `tcp`.

[![Build Status][travis-image]][travis-url]
[![Build Status][appveyor-image]][appveyor-url]

[travis-image]: https://travis-ci.org/paritytech/jsonrpc.svg?branch=master
[travis-url]: https://travis-ci.org/paritytech/jsonrpc
[appveyor-image]: https://ci.appveyor.com/api/projects/status/github/paritytech/jsonrpc?svg=true
[appveyor-url]: https://ci.appveyor.com/project/paritytech/jsonrpc/branch/master

[Documentation](http://paritytech.github.io/jsonrpc/)

## Sub-projects
- [jsonrpc-core](./core) [![crates.io][core-image]][core-url]
- [jsonrpc-http-server](./http) [![crates.io][http-server-image]][http-server-url]
- [jsonrpc-ipc-server](./ipc) [![crates.io][ipc-server-image]][ipc-server-url]
- [jsonrpc-tcp-server](./tcp) [![crates.io][tcp-server-image]][tcp-server-url]
- [jsonrpc-ws-server](./ws) [![crates.io][ws-server-image]][ws-server-url]
- [jsonrpc-stdio-server](./stdio) [![crates.io][stdio-server-image]][stdio-server-url]
- [jsonrpc-macros](./macros) [![crates.io][macros-image]][macros-url] *deprecated:* use `derive` instead
- [jsonrpc-derive](./derive) [![crates.io][derive-image]][derive-url]
- [jsonrpc-server-utils](./server-utils) [![crates.io][server-utils-image]][server-utils-url]
- [jsonrpc-pubsub](./pubsub) [![crates.io][pubsub-image]][pubsub-url]

[core-image]: https://img.shields.io/crates/v/jsonrpc-core.svg
[core-url]: https://crates.io/crates/jsonrpc-core
[http-server-image]: https://img.shields.io/crates/v/jsonrpc-http-server.svg
[http-server-url]: https://crates.io/crates/jsonrpc-http-server
[ipc-server-image]: https://img.shields.io/crates/v/jsonrpc-ipc-server.svg
[ipc-server-url]: https://crates.io/crates/jsonrpc-ipc-server
[tcp-server-image]: https://img.shields.io/crates/v/jsonrpc-tcp-server.svg
[tcp-server-url]: https://crates.io/crates/jsonrpc-tcp-server
[ws-server-image]: https://img.shields.io/crates/v/jsonrpc-ws-server.svg
[ws-server-url]: https://crates.io/crates/jsonrpc-ws-server
[stdio-server-image]: https://img.shields.io/crates/v/jsonrpc-stdio-server.svg
[stdio-server-url]: https://crates.io/crates/jsonrpc-stdio-server
[macros-image]: https://img.shields.io/crates/v/jsonrpc-macros.svg
[macros-url]: https://crates.io/crates/jsonrpc-macros
[derive-image]: https://img.shields.io/crates/v/jsonrpc-derive.svg
[derive-url]: https://crates.io/crates/jsonrpc-derive
[server-utils-image]: https://img.shields.io/crates/v/jsonrpc-server-utils.svg
[server-utils-url]: https://crates.io/crates/jsonrpc-server-utils
[pubsub-image]: https://img.shields.io/crates/v/jsonrpc-pubsub.svg
[pubsub-url]: https://crates.io/crates/jsonrpc-pubsub

## Examples

- [core](./core/examples)
- [derive](./derive/examples)
- [macros](./macros/examples) *deprecated*
- [pubsub](./pubsub/examples)

### Basic Usage (with HTTP transport)

```rust
use jsonrpc_core::{IoHandler, Value, Params};
use jsonrpc_http_server::{ServerBuilder};

fn main() {
	let mut io = IoHandler::new();
	io.add_method("say_hello", |_params: Params| {
		Ok(Value::String("hello".to_string()))
	});

	let server = ServerBuilder::new(io)
		.threads(3)
		.start_http(&"127.0.0.1:3030".parse().unwrap())
		.unwrap();

	server.wait().unwrap();
}
```

### Basic usage with derive

```rust
use jsonrpc_core::Result;
use jsonrpc_derive::rpc;

#[rpc]
pub trait Rpc {
	/// Adds two numbers and returns a result
	#[rpc(name = "add")]
	fn add(&self, u64, u64) -> Result<u64>;
}

pub struct RpcImpl;
impl Rpc for RpcImpl {
	fn add(&self, a: u64, b: u64) -> Result<u64> {
		Ok(a + b)
	}
}

fn main() {
	let mut io = jsonrpc_core::IoHandler::new();
	io.extend_with(RpcImpl.to_delegate())
}
