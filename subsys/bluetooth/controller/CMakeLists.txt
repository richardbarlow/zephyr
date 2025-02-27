# SPDX-License-Identifier: Apache-2.0

zephyr_library()
zephyr_library_sources(
  util/mem.c
  util/memq.c
  util/mayfly.c
  util/util.c
  ticker/ticker.c
  ll_sw/ll_addr.c
  ll_sw/ll_tx_pwr.c
  hci/hci_driver.c
  hci/hci.c
  )

zephyr_library_sources_ifdef(
  CONFIG_BT_CTLR_CRYPTO
  crypto/crypto.c
  )

if(CONFIG_BT_LL_SW_SPLIT)
  zephyr_library_sources(
    ll_sw/ull.c
    ll_sw/lll_common.c
    )
  if(CONFIG_BT_BROADCASTER)
    zephyr_library_sources(
      ll_sw/ull_adv.c
      )
    zephyr_library_sources_ifdef(
      CONFIG_BT_CTLR_ADV_EXT
      ll_sw/ull_adv_aux.c
      )
    zephyr_library_sources_ifdef(
      CONFIG_BT_CTLR_ADV_PERIODIC
      ll_sw/ull_adv_sync.c
      )
    zephyr_library_sources_ifdef(
      CONFIG_BT_CTLR_ADV_ISO
      ll_sw/ull_adv_iso.c
      )
  endif()
  if(CONFIG_BT_OBSERVER)
    zephyr_library_sources(
      ll_sw/ull_scan.c
      )
    zephyr_library_sources_ifdef(
      CONFIG_BT_CTLR_ADV_EXT
      ll_sw/ull_scan_aux.c
      )
    zephyr_library_sources_ifdef(
      CONFIG_BT_CTLR_SYNC_PERIODIC
      ll_sw/ull_sync.c
      )
    zephyr_library_sources_ifdef(
      CONFIG_BT_CTLR_SYNC_ISO
      ll_sw/ull_sync_iso.c
      )
  endif()
  if(CONFIG_BT_CONN)
    zephyr_library_sources(
      ll_sw/ull_conn.c
      )
    if(CONFIG_BT_PERIPHERAL)
      zephyr_library_sources(
        ll_sw/ull_slave.c
      )
    zephyr_library_sources_ifdef(
        CONFIG_BT_CTLR_PERIPHERAL_ISO
        ll_sw/ull_peripheral_iso.c
      )
    endif()
    if(CONFIG_BT_CENTRAL)
      zephyr_library_sources(
        ll_sw/ull_master.c
        )
      zephyr_library_sources_ifdef(
        CONFIG_BT_CTLR_CENTRAL_ISO
        ll_sw/ull_central_iso.c
      )
    endif()
    if(CONFIG_BT_CTLR_SCHED_ADVANCED)
      zephyr_library_sources(
        ll_sw/ull_sched.c
      )
    endif()
  endif()
  if(CONFIG_BT_CTLR_SET_HOST_FEATURE)
    zephyr_library_sources(
      ll_sw/ll_feat.c
      )
  endif()

  if(CONFIG_BT_CTLR_PERIPHERAL_ISO OR
     CONFIG_BT_CTLR_CENTRAL_ISO)
    zephyr_library_sources(
      ll_sw/ull_conn_iso.c
      )
  endif()
  if(CONFIG_BT_CTLR_ADV_ISO OR
     CONFIG_BT_CTLR_SYNC_ISO OR
     CONFIG_BT_CTLR_PERIPHERAL_ISO OR
     CONFIG_BT_CTLR_CENTRAL_ISO)
    zephyr_library_sources(
      ll_sw/ull_iso.c
      ll_sw/isoal.c
      )
  endif()
  if(CONFIG_BT_CONN OR
     CONFIG_BT_CTLR_ADV_PERIODIC OR
     CONFIG_BT_CTLR_SYNC_PERIODIC)
    zephyr_library_sources(
      ll_sw/ull_chan.c
      ll_sw/lll_chan.c
      )
  endif()
  if(CONFIG_BT_CTLR_DF)
    zephyr_library_sources(
      ll_sw/ull_df.c
      )
  endif()
  zephyr_library_sources_ifdef(
    CONFIG_BT_CTLR_FILTER
    ll_sw/ull_filter.c
    )
  zephyr_library_sources_ifdef(
    CONFIG_BT_HCI_MESH_EXT
    ll_sw/ll_mesh.c
    )
endif()

if(CONFIG_SOC_COMPATIBLE_NRF)
  include(ll_sw/nrf.cmake)
elseif(CONFIG_SOC_OPENISA_RV32M1_RISCV32)
  include(ll_sw/openisa.cmake)
endif()

zephyr_library_sources_ifdef(
  CONFIG_BT_CTLR_SETTINGS
  ll_sw/ll_settings.c
)

zephyr_library_include_directories(
  .
  include
  ll_sw
  )

zephyr_library_compile_options_ifdef(
  CONFIG_BT_CTLR_OPTIMIZE_FOR_SPEED
  ${OPTIMIZE_FOR_SPEED_FLAG}
  )

zephyr_library_link_libraries(subsys__bluetooth)
