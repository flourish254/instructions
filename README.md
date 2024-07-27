# Instructions Counter Workshop Basecamp I Kenya

## STEP 1

- `git checkout -b step1 origin/step1`
- `scarb init --name workshop`
- Create file <counter.cairo>

### <counter.cairo> content

`cairo
#[starknet::contract]
mod counter_contract {
    #[storage]
    struct Storage {}
}
`
### <Scarb.toml>
[dependencies]
starknet = ">=2.6.4"

[[target.starknet-contract]]
`cairo
sierra = true
casm = false
casm-add-pythonic-hints = false
`

## STEP 2

- `git checkout -b step2 origin/step2`
- `snforge --version` (use this output for tag value below)
- `[dev-dependencies]
snforge_std = { git = "https://github.com/foundry-rs/starknet-foundry.git", tag = "v0.26.0" }
`
- Change `casm = true`, sierra should be false or else exceptions may be raised that may prevent building the test code
- `[scripts]
test = "snforge test"
`
- Change prelude to 2023_01

## STEP 3

`#[starknet::contract]
mod counter_contract {
    #[storage]
    struct Storage {
        counter: u32 // this line
    }

    #[constructor] // this line
    fn constructor(ref self: ContractState, counter: u32) {
        self.counter.write(counter);
    }
}
`
- mod counter; in lib.cairo

## STEP 4

- `
#[starknet::interface]
trait ICounter<TContractState> {
    fn get_counter(self: @TContractState) -> u32;
}

#[abi(embed_v0)]
impl ICounterImpl of super::ICounter<ContractState>{
    fn get_counter(self: @ContractState) -> u32 {
        self.counter.read()
    }
}
`

## STEP 5

`
#[starknet::interface]
trait ICounter<TContractState> {
    fn get_counter(self: @TContractState) -> u32;
    fn increase_counter(ref self: TContractState);
}

#[abi(embed_v0)]
impl ICounterImpl of super::ICounter<ContractState>{
    fn get_counter(self: @ContractState) -> u32 {
        self.counter.read()
    }

    fn increase_counter(ref self: ContractState) {
        self.counter.write(self.counter.read() + 1);
    }
}
`

## STEP 6

``