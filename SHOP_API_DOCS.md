# Shop API

This document lists all the methods and types that can be used with the game's Shop API.
## Usage
### Adding a shop item
Use `createShopItem(categoryKey, itemKey, item`) to add a shopItem:
```js
api.createShopItem("Special", "Info", {
  image: "info-circle",
  customTitle: "Information",
  description: "This box contains some important information.\njk it doesn't"
})
```
### Configuring a category
Use `api.configureShopCategory(categoryKey, config)` to configure a shop category:
```js
api.configureShopCategory("Special",{
  autoSelectCategory: true
  customTitle: "Super Special" // Supports translation keys and ordinary text
  redDot: false
  forceRemoveRedDot: true
  sortPriority: 3
  description: "An important shop category that is important")
})
```

## Methods
### `ShopItem` methods
```js
/**
 * Create a new shop item under the given category.
 * Will create a new category if it does not exist.
 * If the shop item already exists then it will be replaced.
 * If any per-player overrides exist under the same categoryKey and itemKey then they will be deleted.
 *
 * @param {ShopCategoryKey} categoryKey - The key of the category to create the item in
 * @param {ShopItemKey} itemKey - The unique key for the item
 * @param {ShopItem} item - The shop item to create (will be mutated)
 * @returns {void}
 */
createShopItem(categoryKey, itemKey, item)

/**
 * Update selected properties of an existing shop item.
 * For example, { canBuy: true } to allow players to purchase the item.
 * Throws an error if the item does not exist.
 *
 * @param {ShopCategoryKey} categoryKey - The key of the category containing the item
 * @param {ShopItemKey} itemKey - The unique key for the item
 * @param {Partial<ShopItem>} changes - Partial shop item properties to update
 * @returns {void}
 */
updateShopItem(categoryKey, itemKey, changes)

/**
 * Delete an existing shop item.
 * Throws an error if the item does not exist.
 * Will also delete all per-player overrides for the shop item.
 *
 * @param {ShopCategoryKey} categoryKey - The key of the category containing the item
 * @param {ShopItemKey} itemKey - The unique key for the item
 * @returns {void}
 */
deleteShopItem(categoryKey, itemKey)

/**
 * Create a new shop item for a specific player.
 * Will create a new category if it does not exist.
 * Will replace any overrides this player already has for the same item.
 *
 * @param {PlayerId} playerId - The player to create the item for
 * @param {ShopCategoryKey} categoryKey - The key of the category to create the item in
 * @param {ShopItemKey} itemKey - The unique key for the item
 * @param {ShopItem} item - The shop item to create (will be mutated)
 * @returns {void}
 */
createShopItemForPlayer(playerId, categoryKey, itemKey, item)

/**
 * Update selected properties of an existing shop item for a specific player.
 * For example, { canBuy: true } to allow this player to purchase the item.
 * Throws an error if the item does not exist.
 *
 * @param {PlayerId} playerId - The player to update the item for
 * @param {ShopCategoryKey} categoryKey - The key of the category containing the item
 * @param {ShopItemKey} itemKey - The unique key for the item
 * @param {Partial<ShopItem>} changes - Partial shop item properties to update
 * @returns {void}
 */
updateShopItemForPlayer(playerId, categoryKey, itemKey, changes)

/**
 * Delete a specific player's overrides for a shop item.
 * Like other methods, it doesn't matter whether the overrides were created
 * using createShopItemForPlayer or by using updateShopItemForPlayer instead.
 * This method does nothing if the overrides don't exist or are defined internally by the engine.
 *
 * @param {PlayerId} playerId - The player to reset the item for
 * @param {ShopCategoryKey} categoryKey - The key of the category containing the item
 * @param {ShopItemKey} itemKey - The unique key for the item
 * @returns {void}
 */
resetShopItemForPlayer(playerId, categoryKey, itemKey)
```
### `ShopCategory` methods
```js
/**
 * Set properties of a shop category.
 *
 * @param {ShopCategoryKey} categoryKey - The key of the category to configure
 * @param {ShopCategoryConfig} config - Category configuration properties
 * @returns {void}
 */
configureShopCategory(categoryKey, config)

/**
 * Configure a shop category for a specific player.
 *
 * @param {PlayerId} playerId - The player to configure the category for
 * @param {ShopCategoryKey} categoryKey - The key of the category to configure
 * @param {ShopCategoryConfig} config - Category configuration properties
 * @returns {void}
 */
configureShopCategoryForPlayer(playerId, categoryKey, config)
```
### Miscellaneous methods
```js
/**
 * Show the shop tutorial for a player. Will not be shown if they have ever seen the shop tutorial in your game before.
 *
 * @param {PlayerId} playerId
 * @returns {void}
 */
showShopTutorial(playerId)

/**
 * Show a message over the shop in the same place that a shop item's onBoughtMessage is shown.
 * Displays for a couple seconds before disappearing
 * Use case is to show a dynamic message when player buys an item
 *
 * @param {PlayerId} playerId
 * @param {string | CustomTextStyling} info
 * @returns {void}
 */
sendOverShopInfo(playerId, info)

/**
 * Open the shop UI for a player
 *
 * @param {PlayerId} playerId
 * @param {boolean} [toggle] - Whether to close the shop if it's already open
 * @param {PNull<ShopCategoryKey>} [forceCategoryKey] - If set, will change the shop to this category
 * @param {boolean} [onlyIfNonEmpty] - If true, will only open the shop if the category (or shop, if no category is provided) is non-empty
 * @returns {void}
 */
openShop(playerId, toggle, forceCategoryKey, onlyIfNonEmpty)
```

## Callback

```js
/**
 * Called after a player successfully buys a shop item
 * @param {PlayerId} playerId - The id of the player that bought the item
 * @param {ShopCategoryKey} categoryKey - The shop category key
 * @param {ShopItemKey} itemKey - The shop item key
 * @param {BoughtShopItem} item - The resolved shop item (with per-player overrides applied, internal properties stripped)
 * @param {string} [userInput] - The user input provided, if the item has a userInput config
 */
onPlayerBoughtShopItem = (playerId: PlayerId, categoryKey: ShopCategoryKey, itemKey: ShopItemKey, item: BoughtShopItem, userInput?: string) => {}
```

## Referenced Types:
```ts
type ShopItem = {
    image: string
    cost?: number // Only for the special currencies like Coal and Diamond found on the bottom-left of shop in gamemodes like Bedwars,
    currency?: string // Same thing as cost, only for special currencies. Note: there is currently no way to use these special currencies.
    amount?: number // Display amount shown on the shop tile image (0 and 1 are not displayed)
    imageColour?: string
    canBuy?: boolean
    isSelected?: boolean
    buyButtonText?: string | CustomTextStyling
    customTitle?: string | CustomTextStyling
    description?: string | CustomTextStyling
    onBoughtMessage?: string | CustomTextStyling
    redDot?: boolean // The red "unread" dot that indicates "New Item Available" in Bedwars
    forceRemoveRedDot?: boolean
    badge?: { text: string | CustomTextStyling; type: "new" | "lucky" }
    userInput?: ShopItemUserInput
    sell?: boolean // Optional, defaults to false. If true, the sign of "cost" is flipped. So a "cost" of -25 would give the player 25 currency AND be displayed as "25" (instead of -25)
    sortPriority?: number // Descending, bigger number means closer to the top
    hidden?: boolean
}

type ShopItemUserInput =
    | { type: "text"; placeholderText?: string; wordCharsOnly?: boolean } // Defaults to false. If true, only allows \w character (alphanumeric and _)
    | { type: "number"; placeholderText?: string }
    | { type: "dropdown"; dropdownOptions: readonly (string | { option: string; cost: number })[]; shouldResetSelectionOnOptionsChange?: boolean } // Defaults to false. If true, the selection will reset to the first option when dropdownOptions changes
    | { type: "player"; excludedPlayers?: PlayerId[] } // Defaults to excluding the current player
    | { type: "color" }

type ShopCategoryConfig = Partial<{
    autoSelectCategory: boolean
    customTitle: string // Supports translation keys and ordinary text
    redDot: boolean
    forceRemoveRedDot: boolean
    sortPriority: number
    description: string | CustomTextStyling
}>
```
## Helpers

|Name|Description|Link|
|:-:|:-:|:-:|
|ats - add to shop|Easily make a custom shop with buy and sell for multiple items and rngs|[ats](/ats.js)|
