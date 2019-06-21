# Tip-bit2

### Install

`npm i bit-bin -g`



### Init

```bash
cd directory
bit init
```



### Track files

`bit add src/components/*`



### Defining a Compiler

`bit import bit.envs/compilers/babel —compiler`



### Defining a testing framework

`bit import bit.envs/testers/jest —tester`



### Tagging

- `bit tag —all` update patch, first version @0.0.1
- `bit tag -all --patch` update patch
- `bit tag -all --minor` update minor
- `bit tag -all --major` update major
- `bit tag -all @1.0.0` appoint version



### Export

```bash
bit login
bit export [account_name].[collection_name] // bit export dunz.test
```



### Use other project

```
npm config set '@bit:registry' https://node.bit.dev
npm i @bit/[account_name].[collection_name].[component_name]
```
> SELF_SIGNED_CERT_IN_CHAIN 에러 발생시 `npm config set strict-ssl false`설정을 해준다



### Develop components from multiple projects

```
bit init
bit import [account_name].[collection_name]/[component_name]
```

> `bit init` 을 하게되면 .bit, .bit.map 파일이 생기는데 .gitignore에 추가하여 무시하거나 eject 후 삭제하면 된다.



### Eject Bit components after complete export

```
bit eject [component_name]
```



### Checkout Bit component

```
bit checkout --all [version]
```
