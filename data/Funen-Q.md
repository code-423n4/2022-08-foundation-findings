1. Lack of Event 

on [AdminRole.sol](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/roles/AdminRole.sol) needed to use event for fn `grantAdmin()` and fn `revokeAdmin()` to shown that function are only executable by privileged users (e.g. onlyAdmin) and have an impact  on other users should emit events. Events can be used on Minter role too


2. Avoid Floatin Pragma's

Since it was used ^0.8.12. As the compiler can be use for example 0.8.x and consider locking at this version the same as another. It can be consider using  locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. Since it can be problematic, if there are publicly disclosed bugs and issues that affect the current compiler version used.

