# Exchange Events

The exchange module emits several events for various actions.

---
<!-- TOC -->
  - [EventOrderCreated](#eventordercreated)
  - [EventOrderCancelled](#eventordercancelled)
  - [EventOrderFilled](#eventorderfilled)
  - [EventOrderPartiallyFilled](#eventorderpartiallyfilled)
  - [EventOrderExternalIDUpdated](#eventorderexternalidupdated)
  - [EventFundsCommitted](#eventfundscommitted)
  - [EventCommitmentReleased](#eventcommitmentreleased)
  - [EventMarketWithdraw](#eventmarketwithdraw)
  - [EventMarketDetailsUpdated](#eventmarketdetailsupdated)
  - [EventMarketOrdersEnabled](#eventmarketordersenabled)
  - [EventMarketOrdersDisabled](#eventmarketordersdisabled)
  - [EventMarketUserSettleEnabled](#eventmarketusersettleenabled)
  - [EventMarketUserSettleDisabled](#eventmarketusersettledisabled)
  - [EventMarketCommitmentsEnabled](#eventmarketcommitmentsenabled)
  - [EventMarketCommitmentsDisabled](#eventmarketcommitmentsdisabled)
  - [EventMarketIntermediaryDenomUpdated](#eventmarketintermediarydenomupdated)
  - [EventMarketPermissionsUpdated](#eventmarketpermissionsupdated)
  - [EventMarketReqAttrUpdated](#eventmarketreqattrupdated)
  - [EventMarketCreated](#eventmarketcreated)
  - [EventMarketFeesUpdated](#eventmarketfeesupdated)
  - [EventParamsUpdated](#eventparamsupdated)
  - [EventPaymentCreated](#eventpaymentcreated)
  - [EventPaymentUpdated](#eventpaymentupdated)
  - [EventPaymentAccepted](#eventpaymentaccepted)
  - [EventPaymentRejected](#eventpaymentrejected)
  - [EventPaymentCancelled](#eventpaymentcancelled)


## EventOrderCreated

Any time an order is created, an `EventOrderCreated` is emitted.

Event Type: `provenance.exchange.v1.EventOrderCreated`

| Attribute Key | Attribute Value                                           |
|---------------|-----------------------------------------------------------|
| order_id      | The id of the order just created.                         |
| order_type    | The type of the order just created (e.g. "ask" or "bid"). |
| market_id     | The id of the market that the order was created in.       |
| external_id   | The external id of the order just created.                |


## EventOrderCancelled

When an order is cancelled (either by the owner or the market), an `EventOrderCancelled` is emitted.

Event Type: `provenance.exchange.v1.EventOrderCancelled`

| Attribute Key | Attribute Value                                             |
|---------------|-------------------------------------------------------------|
| order_id      | The id of the cancelled order.                              |
| cancelled_by  | The bech32 address of the account that cancelled the order. |
| market_id     | The id of the market that the cancelled order was in.       |
| external_id   | The external id of the order that was just cancelled.       |


## EventOrderFilled

When an order is filled in full, an `EventOrderFilled` is emitted.

This event indicates that an order has been settled, cleared, and deleted.

Event Type: `provenance.exchange.v1.EventOrderFilled`

| Attribute Key | Attribute Value                                      |
|---------------|------------------------------------------------------|
| order_id      | The id of the settled order.                         |
| assets        | The assets that were bought or sold (`Coin` string). |
| price         | The price received (`Coin` string).                  |
| fees          | The fees paid to settle the order (`Coins` string).  |
| market_id     | The id of the market that the order was in.          |
| external_id   | The external id of the order.                        |

The `assets`, `price`, and `fees`, reflect the funds that were actually transferred.
E.g. when an ask order is settled for a higher price than set in the order, the `price` reflects what the seller actually received.
Similarly, the `fees` reflect the actual settlement fees paid (both flat and ratio) by the order's owner.

If an order was previously partially filled, but now, the rest is being filled, this event is emitted.


## EventOrderPartiallyFilled

When an order is partially filled, an `EventOrderPartiallyFilled` is emitted.

This event indicates that some of an order was filled, but that the order has been reduced and still exists.

Event Type: `provenance.exchange.v1.EventOrderPartiallyFilled`

| Attribute Key | Attribute Value                                                          |
|---------------|--------------------------------------------------------------------------|
| order_id      | The id of the partially settled order.                                   |
| assets        | The assets that were bought or sold (`Coin` string).                     |
| price         | The price received (`Coin` string).                                      |
| fees          | The fees paid for the partial settlement of this order (`Coins` string). |
| market_id     | The id of the market that the order is in.                               |
| external_id   | The external id of the order.                                            |

The `assets`, `price`, and `fees`, reflect the funds that were actually transferred.

If an order was previously partially filled, but now, the rest is being filled, an `EventOrderFilled` is emitted.


## EventOrderExternalIDUpdated

When an order's external id is updated, an `EventOrderExternalIDUpdated` is emitted.

Event Type: `provenance.exchange.v1.EventOrderExternalIDUpdated`

| Attribute Key  | Attribute Value                            |
|----------------|--------------------------------------------|
| order_id       | The id of the updated order.               |
| market_id      | The id of the market that the order is in. |
| external_id    | The new external id of the order.          |


## EventFundsCommitted

When funds are committed to a market by an account, an `EventFundsCommitted` is emitted.

Event Type: `provenance.exchange.v1.EventFundsCommitted`

| Attribute Key | Attribute Value                                             |
|---------------|-------------------------------------------------------------|
| account       | The bech32 address of the account that committed the funds. |
| market_id     | The id of the market that funds were committed to.          |
| amount        | The funds committed (`Coins` string).                       |
| tag           | The `event_tag` provided in the msg.                        |


## EventCommitmentReleased

When funds are released by a market, an `EventCommitmentReleased` is emitted.

Event Type: `provenance.exchange.v1.EventCommitmentReleased`

| Attribute Key | Attribute Value                                             |
|---------------|-------------------------------------------------------------|
| account       | The bech32 address of the account that committed the funds. |
| market_id     | The id of the market that funds were committed to.          |
| amount        | The funds committed (`Coins` string).                       |
| tag           | The `event_tag` provided in the msg.                        |

## EventMarketWithdraw

Any time a market's funds are withdrawn, an `EventMarketWithdraw` is emitted.

Event Type: `provenance.exchange.v1.EventMarketWithdraw`

| Attribute Key | Attribute Value                                                          |
|---------------|--------------------------------------------------------------------------|
| market_id     | The id of the market the funds were withdrawn from.                      |
| amount        | The funds withdrawn (`Coins` string).                                    |
| destination   | The bech32 address string of the account that received the funds.        |
| withdrawn_by  | The bech32 address string of the admin account that made the withdrawal. |


## EventMarketDetailsUpdated

When a market's details are updated, an `EventMarketDetailsUpdated` is emitted.

Event Type: `provenance.exchange.v1.EventMarketDetailsUpdated`

| Attribute Key | Attribute Value                                                       |
|---------------|-----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                         |
| updated_by    | The bech32 address string of the admin account that made the change.  |


## EventMarketOrdersEnabled

When a market's `accepting_orders` changes from `false` to `true`, an `EventMarketOrdersEnabled` is emitted.

Event Type: `provenance.exchange.v1.EventMarketOrdersEnabled`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketOrdersDisabled

When a market's `accepting_orders` changes from `true` to `false`, an `EventMarketOrdersDisabled` is emitted.

Event Type: `provenance.exchange.v1.EventMarketOrdersDisabled`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketUserSettleEnabled

When a market's `allow_user_settlement` changes from `false` to `true`, an `EventMarketUserSettleEnabled` is emitted.

Event Type: `provenance.exchange.v1.EventMarketUserSettleEnabled`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketUserSettleDisabled

When a market's `allow_user_settlement` changes from `true` to `false`, an `EventMarketUserSettleDisabled` is emitted.

Event Type: `provenance.exchange.v1.EventMarketUserSettleDisabled`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketCommitmentsEnabled

When a market's `accepting_commitments` changes from `false` to `true`, an `EventMarketCommitmentsEnabled` is emitted.

Event Type: `provenance.exchange.v1.EventMarketCommitmentsEnabled`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketCommitmentsDisabled

When a market's `accepting_commitments` changes from `true` to `false`, an `EventMarketCommitmentsDisabled` is emitted.

Event Type: `provenance.exchange.v1.EventMarketCommitmentsDisabled`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketIntermediaryDenomUpdated

When a market's `intermediary_denom` is updated, an `EventMarketIntermediaryDenomUpdated` is emitted.

Event Type: `provenance.exchange.v1.EventMarketIntermediaryDenomUpdated`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketPermissionsUpdated

Any time a market's permissions are managed, an `EventMarketPermissionsUpdated` is emitted.

Event Type: `provenance.exchange.v1.EventMarketPermissionsUpdated`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketReqAttrUpdated

When a market's required attributes are altered, an `EventMarketReqAttrUpdated` is emitted.

Event Type: `provenance.exchange.v1.EventMarketReqAttrUpdated`

| Attribute Key | Attribute Value                                                      |
|---------------|----------------------------------------------------------------------|
| market_id     | The id of the updated market.                                        |
| updated_by    | The bech32 address string of the admin account that made the change. |


## EventMarketCreated

When a market is created, an `EventMarketCreated` is emitted.

Event Type: `provenance.exchange.v1.EventMarketCreated`

| Attribute Key | Attribute Value           |
|---------------|---------------------------|
| market_id     | The id of the new market. |


## EventMarketFeesUpdated

When a market's fees are updated, an `EventMarketFeesUpdated` is emitted.

Event Type: `provenance.exchange.v1.EventMarketFeesUpdated`

| Attribute Key | Attribute Value               |
|---------------|-------------------------------|
| market_id     | The id of the updated market. |


## EventParamsUpdated

An `EventParamsUpdated` is emitted when the exchange module's params are changed.

Event Type: `provenance.exchange.v1.EventParamsUpdated`

| Attribute Key | Attribute Value |
|---------------|-----------------|
| (none)        |                 |


## EventPaymentCreated

When a payment is created, an `EventPaymentCreated` is emitted.

Event Type: `provenance.exchange.v1.EventPaymentCreated`

| Attribute Key | Attribute Value                                                             |
|---------------|-----------------------------------------------------------------------------|
| source        | The bech32 address string of the source account (that created the payment). |
| source_amount | The amount in the source account involved in the payment (`Coins` string).  |
| target        | The bech32 address string of the target account.                            |
| target_amount | The amount that the target account will provide (`Coins` string).           |
| external_id   | The external id of the payment just created.                                |


## EventPaymentUpdated

When a payment's target is changed, an `EventPaymentUpdated` is emitted.

Event Type: `provenance.exchange.v1.EventPaymentUpdated`

| Attribute Key | Attribute Value                                                             |
|---------------|-----------------------------------------------------------------------------|
| source        | The bech32 address string of the source account (that updated the payment). |
| source_amount | The amount in the source account involved in the payment (`Coins` string).  |
| old_target    | The old bech32 address string of the target account.                        |
| new_target    | The new bech32 address string of the target account.                        |
| target_amount | The amount that the target account will provide (`Coins` string).           |
| external_id   | The external id of the payment just updated.                                |


## EventPaymentAccepted

When a payment is accepted, an `EventPaymentAccepted` is emitted.

Event Type: `provenance.exchange.v1.EventPaymentAccepted`

| Attribute Key | Attribute Value                                                              |
|---------------|------------------------------------------------------------------------------|
| source        | The bech32 address string of the source account (that created the payment).  |
| source_amount | The amount in the source account involved in the payment (`Coins` string).   |
| target        | The bech32 address string of the target account (that accepted the payment). |
| target_amount | The amount that the target account will provide (`Coins` string).            |
| external_id   | The external id of the payment just accepted.                                |


## EventPaymentRejected

When a payment is rejected (by a target), an `EventPaymentRejected` is emitted.

Event Type: `provenance.exchange.v1.EventPaymentRejected`

| Attribute Key | Attribute Value                                                              |
|---------------|------------------------------------------------------------------------------|
| source        | The bech32 address string of the source account (that created the payment).  |
| target        | The bech32 address string of the target account (that rejected the payment). |
| external_id   | The external id of the payment just accepted.                                |


## EventPaymentCancelled

When a payment is cancelled (by a source), an `EventPaymentCancelled` is emitted.

Event Type: `provenance.exchange.v1.EventPaymentCancelled`

| Attribute Key | Attribute Value                                                               |
|---------------|-------------------------------------------------------------------------------|
| source        | The bech32 address string of the source account (that cancelled the payment). |
| target        | The bech32 address string of the target account.                              |
| external_id   | The external id of the payment just accepted.                                 |
