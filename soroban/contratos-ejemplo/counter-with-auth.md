# Counter with Auth



```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, contracttype, Address, Env};

#[contracttype]
pub enum DataKey {
    Counter(Address),
}

#[contract]
pub struct IncrementContract;

#[contractimpl]
impl IncrementContract {
    /// Increment increments a counter for the user, and returns the value.
    pub fn increment(env: Env, user: Address, value: u32) -> (Address, u32) {
        // Requires user to have authorized call of the increment of this
        // contract with all the arguments passed to increment, i.e. user
        // and value. This will panic if auth fails for any reason.
        // When this is called, Soroban host performs the necessary
        // authentication, manages replay prevention and enforces the user's
        // authorization policies.
        // The contracts normally shouldn't worry about these details and just
        // write code in generic fashion using Address and require_auth (or
        // require_auth_for_args).
        user.require_auth();

        // This call is equilvalent to the above:
        // user.require_auth_for_args((&user, value).into_val(&env));

        // The following has less arguments but is equivalent in authorization
        // scope to the above calls (the user address doesn't have to be
        // included in args as it's guaranteed to be authenticated).
        // user.require_auth_for_args((value,).into_val(&env));

        // Construct a key for the data being stored. Use an enum to set the
        // contract up well for adding other types of data to be stored.
        let key = DataKey::Counter(user.clone());

        // Get the current count for the invoker.
        let mut count: u32 = env.storage().persistent().get(&key).unwrap_or_default();

        // Increment the count.
        count += value;

        // Save the count.
        env.storage().persistent().set(&key, &count);

        // Return the count to the caller.
        (user, count)
    }
}
```

Explicación del código



<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>
