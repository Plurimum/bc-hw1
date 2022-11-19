# Курс “Блокчейн” |  Д/З №1
# Задание 1.1
сделать, чтобы с баланса multisig-контракта за одну транзакцию не могло бы уйти больше, чем 66 ETH

```solidity
@@ -22,6 +22,7 @@ contract MultiSigWallet {
      *  Constants
      */
     uint constant public MAX_OWNER_COUNT = 50;
+    uint constant public MAX_TRANSACTION_VALUE = 66 ether;
 
     /*
      *  Storage
@@ -91,6 +92,11 @@ contract MultiSigWallet {
         _;
     }
 
+    modifier validTransactionValue(uint value) {
+        require(value <= MAX_TRANSACTION_VALUE);
+        _;
+    }

     /// @dev Fallback function allows to deposit ether.
     function()
         payable
@@ -188,6 +194,7 @@ contract MultiSigWallet {
     /// @return Returns transaction ID.
     function submitTransaction(address destination, uint value, bytes data)
         public
+        validTransactionValue(value)
         returns (uint transactionId)
     {
         transactionId = addTransaction(destination, value, data);
```

# Задание 1.2
сделать, чтобы токен не мог бы быть transferred по субботам

```solidity 
@@ -52,7 +52,12 @@ contract ERC20 is Context, IERC20 {
         _name = name_;
         _symbol = symbol_;
     }
-
+    
+    modifier itIsNotSaturday {
+        require((now / (1 days) - 2) % 7 != 0);
+        _;
+    }
+    
     /**
      * @dev Returns the name of the token.
      */
@@ -205,7 +210,10 @@ contract ERC20 is Context, IERC20 {
      * - `recipient` cannot be the zero address.
      * - `sender` must have a balance of at least `amount`.
      */
-    function _transfer(address sender, address recipient, uint256 amount) internal virtual {
+    function _transfer(address sender, address recipient, uint256 amount) 
+        internal virtual 
+        itIsNotSaturday
+    {
         require(sender != address(0), "ERC20: transfer from the zero address");
         require(recipient != address(0), "ERC20: transfer to the zero address");
 

```
