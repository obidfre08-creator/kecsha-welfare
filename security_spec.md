# Security Specification for Kilifi County KECSHA Welfare Association

## 1. Data Invariants
1. Member profiles must contain valid unique IDs and required details (`id`, `name`, `school`).
2. Contribution records must reference a member ID (`m`), month, positive amount, and valid status.
3. Welfare claims must include an authorized claim type, claimant identity, valid amount, and state.
4. Disbursements can only be recorded for approved cases with payment references.
5. All operations are audited with timestamped actor logs.
6. Real-time synchronisation across all devices must maintain data integrity and prevent unauthorized modifications.

## 2. Dirty Dozen Security Payloads
1. **Invalid ID string injection**: `{ id: "../../../etc/passwd" }` -> Denied by `isValidId`.
2. **Gigantic ID length overflow**: ID string > 128 chars -> Denied by `isValidId`.
3. **Negative contribution amount**: `{ amt: -5000 }` -> Rejected by validation rules.
4. **Altering closed audit logs**: Updates to historical audit entries -> Disallowed.
5. **Orphan contribution**: Contribution without a valid member ID -> Rejected.
6. **Self-assigned admin override without verification**: Spoofed admin payload -> Denied.
7. **Direct status mutation to 'Paid' bypassing approvals**: Direct update without Chairperson sign-off -> Rejected.
8. **Null member payload write**: `{}` to `/members/KCW-999` -> Blocked.
9. **Exceeding maximum field sizes**: 2MB raw payloads -> Blocked.
10. **Spoofed payment voucher injection**: Payout record referencing non-existent case -> Blocked.
11. **Altering locked constitution or settings**: Unsanitized parameter overwrite -> Rejected.
12. **Malformed notification injection**: Ghost notifications with invalid roles -> Rejected.

## 3. Test Runner
Verification confirmed through end-to-end integration and Firestore security checks.
