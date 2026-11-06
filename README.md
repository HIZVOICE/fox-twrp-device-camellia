# OrangeFox Recovery for Redmi Note 10 5G (camellia) — decryption-fix build

Unofficial OrangeFox device tree for the Xiaomi Redmi Note 10 5G / Note 10 5G
Global (camellia / camellian, MediaTek MT6833), fox_12.1 lineage — with the
**FBE decryption fix set applied**. The maintainer's earlier build of this
lineage (2026-08-14) boots and its touch works, but it cannot decrypt /data:
it predates every one of these settings. This tree adds them:

- `OF_DEFAULT_KEYMASTER_VERSION=4.1` (OrangeFox-documented; camellia's
  keymaster is the 4.1 beanpod, and the broken build's recovery logs that no
  default was set)
- `OF_FIX_DECRYPTION_ON_DATA_MEDIA=1` (OrangeFox-documented for
  decryption failure on sdcard-on-data devices like this one)
- `TW_USE_FSCRYPT_POLICY=2`, `TW_CRYPTO_USE_SYSTEM_VOLD=true`,
  `TARGET_PROVIDES_KEYMASTER=true`, `OFOX_DONT_WIPE_FBE_METADATA=1`

Also carried in: the Flyme prebuilt kernel from the verified PallzNtDv TWRP
build for this device (replacing the tree's unused stock prebuilt), and the
touch wake watchdog pair taken byte-identical from the working donor image
(backlight-poll variant — the tree's older fb0-poll script cannot fire on MTK).

**Maintainer:** hisvoice · **Status:** Beta / bring-up · **OrangeFox branch:** fox_12.1
**Credit:** based on PallzNtDv/fox-twrp-device-camellia; the decryption deltas
mirror rwxrx-rx's post-2026-08-21 camellia fox_12.1 fixes plus OrangeFox's
documented build defaults.

## Bootstrap layout (temporary)

For the first build, the device tree ships as **`tree.zip`** at this repo's
root; the build workflow unpacks it into `device/xiaomi/camellia` inside the
synced OrangeFox source. The zip will be expanded into a regular file tree
in a later cleanup — the tree contents are identical either way.

## Building

GitHub Actions → **Build OrangeFox Recovery** → Run workflow
(defaults: branch `fox_12.1`, `device/xiaomi/camellia`, `bootimage`).
The build publishes a release with the OrangeFox flashable zip and
`recovery.img` (the raw boot image). Flash the image to **boot** (camellia
is virtual A/B, recovery-as-boot; there is no recovery partition):
`fastboot flash boot boot.img`

## Honest status

- Decryption (FBE, Microtrust TEE): the fix set above is **UNPROVEN** until
  it is tested on the phone — no camellia OrangeFox build, fixed or not, has
  a public on-device decryption report yet.
- Touch: donor watchdog pair included (see above); expected to behave like
  the current, working-touch build.
- The data on the test phone was encrypted by a HyperOS 2.0 port ROM; if a
  known-good TWRP also fails to decrypt that ROM, the blocker is on the ROM
  side and this build inherits that dependency.

Keep the verified TWRP boot.img from the camellia TWRP build handy before
flashing — it is the one-command way back.
