# Voucher Stock UAT Checklist

## Import and Upload

- [ ] Upload CSV with duplicate `secret` values → API rejects with `IMPORT_DUPLICATE_SECRET`.
- [ ] Import form preview displays: order id, order date, quantity, min serial, max serial.
- [ ] Submit action is visible and labeled in English (e.g. **Submit**).
- [ ] Imported request list shows order date and serial range fields.

## Order Update Policy

- [ ] Edit order id allowed only when request state is inactive.
- [ ] Edit order id blocked after activation with `ORDER_ID_EDIT_NOT_ALLOWED_STATE`.
- [ ] Modification remarks are required for order id edits.

## Transfer and Export

- [ ] Branch transfer preview shows requested and available quantities.
- [ ] Requesting quantity above available fails with `EXPORT_INSUFFICIENT_STOCK` and no partial transfer.
- [ ] Transfer confirmation appears before execution.
- [ ] Monthly open custody transfer rule blocks second transfer with `CUSTODY_TRANSFER_BLOCKED_MONTHLY_OPEN_TRANSFER`.

## Reports and Filters

- [ ] Stock transactions report accepts `orderId` and `serialNumber` filters.
- [ ] Stock transaction statistics accepts `orderId` and `serialNumber` filters.
- [ ] CSV/Excel export action available on transactions/statistics reports.
- [ ] Order lookup export endpoint returns CSV attachment.
- [ ] Card balance export endpoint returns CSV attachment.

## Permissions and Logs

- [ ] `GET /api/logs` returns explicit fields (`user_id`, `action_type`, `target_entity_id`, `remarks`, `timestamp`).
- [ ] Logs page renders paginated entries and basic filters.
- [ ] Search users by permission endpoint returns matching users.
- [ ] Report endpoint permissions use granular policies.

## Localization

- [ ] UI copy is English across primary flows (menus, buttons, validation messages).
- [ ] i18n resource files exist under `vouchers-stock/src/assets/i18n` if multi-language is enabled.
