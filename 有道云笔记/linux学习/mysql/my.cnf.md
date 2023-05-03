```shell
[mysqld]

#rds_user_with_kill_option = xx

socket = /usr/local/mysql/tmp/mysql.sock

datadir = /usr/local/mysql/data

pid-file = /usr/local/mysql/data/mysql.pid

tmpdir = /usr/local/mysql/tmp

port = 3306

back_log = 3000

character_set_server = utf8

max_connect_errors = 100

max_connections = 5532

max_user_connections = 5000

#rds_reserved_connections = 512

#maintain_user_list = 'aliyun_root,aurora,replicator'

max_heap_table_size = 67108864

max_allowed_packet = 1024M

max_binlog_size = 500M

thread_stack = 262144

interactive_timeout = 7200

wait_timeout = 86400

sort_buffer_size = 848KB

read_buffer_size = 848KB

read_rnd_buffer_size = 432KB

join_buffer_size = 432KB

net_buffer_length = 16384

thread_cache_size = 256

ft_min_word_len = 4

transaction_isolation = READ-COMMITTED

tmp_table_size = 2097152

table_open_cache = 2000

skip_name_resolve

core-file

lower_case_table_names = 1

log_bin_trust_function_creators = 1

log-bin = /usr/local/mysql/mysql/mysql-bin.log

log-bin-index = /usr/local/mysql/mysql/master-log-bin.index

log-error = /usr/local/mysql/mysql/master-error.log

relay-log = /usr/local/mysql/mysql/slave-relay.log

relay-log-info-file = /usr/local/mysql/mysql/slave-relay-log.info

relay-log-index = /usr/local/mysql/mysql/slave-relay-log.index

master-info-file = /usr/local/mysql/mysql/master.info

log-slave-updates = 1

binlog_cache_size = 2048KB

sync_binlog = 1

log_warnings

slow_query_log_file = /usr/local/mysql/mysql/slow_query.log

slow_query_log = ON

log_output = TABLE

long_query_time = 1

binlog_format = ROW

server_id = 956927685

auto_increment_increment = 1

auto_increment_offset = 1

slave_net_timeout = 60

key_buffer_size = 16M

bulk_insert_buffer_size = 4194304

myisam_sort_buffer_size = 262144

myisam_max_sort_file_size = 2048K

myisam_repair_threads = 1

myisam_recover_options = FORCE

innodb_data_home_dir = /usr/local/mysql/mysql

innodb_log_group_home_dir = /usr/local/mysql/mysql

innodb_buffer_pool_size = 24576M

innodb_data_file_path = ibdata1:200M:autoextend

innodb_file_per_table

innodb_flush_log_at_trx_commit = 1

innodb_log_buffer_size = 8M

innodb_log_file_size = 1500M

innodb_log_files_in_group = 2

innodb_max_dirty_pages_pct = 75

innodb_flush_method = O_DIRECT

innodb_lock_wait_timeout = 50

innodb_doublewrite = 1

innodb_rollback_on_timeout = OFF

innodb_autoinc_lock_mode = 2

innodb_read_io_threads = 4

innodb_write_io_threads = 4

innodb_io_capacity = 20000

innodb_purge_threads = 1

master_info_repository = TABLE

relay_log_info_repository = TABLE

query_cache_type = 0

concurrent_insert = 1

query_cache_limit = 1048576

query_cache_min_res_unit = 1024

log-slow-admin-statements

innodb_stats_on_metadata = OFF

innodb_file_format = Barracuda

innodb_thread_concurrency = 0

innodb_sync_spin_loops = 100

innodb_spin_wait_delay = 6

default_storage_engine = InnoDB

innodb_stats_sample_pages = 8

open_files_limit = 65535

gtid_mode = ON

enforce-gtid-consistency = 1

loose_performance_schema = off

loose_binlog_order_commits = OFF

innodb_ft_max_token_size = 84

loose_tokudb_cache_size = 26215M

loose_show_ipk_info = OFF

loose_rpl_semi_sync_master_enabled = ON

log_bin_use_v1_row_events = 1

innodb_flush_sync = ON

key_cache_division_limit = 100

innodb_purge_rseg_truncate_frequency = 128

loose_rpl_semi_sync_master_timeout = 1000

min_examined_row_limit = 0

disconnect_on_expired_password = ON

innodb_stats_method = nulls_equal

max_join_size = 18446744073709500000

show_compatibility_56 = OFF

innodb_read_ahead_threshold = 56

innodb_compression_failure_threshold_pct = 5

end_markers_in_json = OFF

innodb_sync_array_size = 1

max_points_in_geometry = 65536

session_track_gtids = OFF

loose_innodb_log_compressed_pages = OFF

innodb_adaptive_flushing_lwm = 10

loose_opt_enable_rds_priv_strategy = ON

innodb_change_buffer_max_size = 25

session_track_schema = ON

loose_innodb_data_file_purge_interval = 100

innodb_ft_enable_stopword = ON

innodb_print_all_deadlocks = OFF

innodb_ft_enable_diag_print = OFF

loose_optimizer_trace = enabled=off,one_line=off

delayed_insert_timeout = 300

loose_rpl_semi_sync_slave_enabled = ON

old_passwords = 0

max_error_count = 64

table_definition_cache = 512

innodb_log_checksums = ON

innodb_buffer_pool_load_at_startup = ON

loose_thread_pool_oversubscribe = 16

query_cache_size = 3145728

loose_max_statement_time = 0

innodb_max_purge_lag_delay = 0

preload_buffer_size = 32768

loose_optimizer_trace_features = greedy_search=on,range_optimizer=on,dynamic_range=on,repeated_subselect=on

innodb_ft_cache_size = 8000000

metadata_locks_cache_size = 1024

loose_keyring_rds_kms_agent_cmd = /home/mysql/kms_agent

binlog_stmt_cache_size = 32768

optimizer_trace_limit = 1

loose_performance_agent_interval = 1

automatic_sp_privileges = ON

flush_time = 0

loose_rpl_semi_sync_master_wait_point = AFTER_SYNC

disabled_storage_engines = myisam,memory,archive

innodb_buffer_pool_instances = 1

net_retry_count = 10

binlog_checksum = CRC32

loose_innodb_numa_interleave = ON

low_priority_updates = 0

innodb_max_purge_lag = 0

log_throttle_queries_not_using_indexes = 0

loose_rpl_semi_sync_master_trace_level = 1

loose_innodb_data_file_purge = ON

optimizer_trace_max_mem_size = 16384

optimizer_trace_offset = -1

loose_rds_set_connection_id_enabled = ON

innodb_adaptive_max_sleep_delay = 150000

innodb_old_blocks_pct = 37

max_sp_recursion_depth = 0

delayed_queue_size = 1000

key_cache_age_threshold = 300

innodb_concurrency_tickets = 5000

range_optimizer_max_mem_size = 8388608

innodb_buffer_pool_dump_at_shutdown = ON

loose_performance_agent_file_size = 100

loose_performance_point_iostat_volume_size = 10000

innodb_stats_persistent = ON

loose_rds_check_core_file_enabled = ON

host_cache_size = 644

innodb_use_native_aio = OFF

ngram_token_size = 2

block_encryption_mode = "aes-128-ecb"

innodb_status_output_locks = OFF

master_verify_checksum = OFF

event_scheduler = OFF

loose_innodb_data_file_purge_max_size = 512

lock_wait_timeout = 31536000

innodb_rollback_segments = 128

net_write_timeout = 60

sha256_password_proxy_users = OFF

innodb_table_locks = ON

updatable_views_with_limit = YES

innodb_ft_total_cache_size = 640000000

innodb_lru_scan_depth = 1024

innodb_random_read_ahead = OFF

loose_performance_point_iostat_interval = 2

stored_program_cache = 256

innodb_open_files = 3000

loose_rpl_semi_sync_master_wait_no_slave = OFF

max_prepared_stmt_count = 16382

loose_rds_kill_connections = 20

innodb_change_buffering = all

loose_rds_force_memory_to_innodb = ON

character_set_filesystem = binary

autocommit = ON

net_read_timeout = 30

optimizer_prune_level = 1

lc_time_names = en_US

max_write_lock_count = 102400

session_track_state_change = OFF

innodb_old_blocks_time = 1000

innodb_max_dirty_pages_pct_lwm = 0

loose_implicit_primary_key = 1

loose_opt_rds_enable_show_slave_lag = ON

innodb_sort_buffer_size = 1048576

ft_max_word_len = 84

innodb_page_cleaners = 1

max_length_for_sort_data = 1024

loose_innodb_index_pct_free = 10

mysql_native_password_proxy_users = OFF

max_binlog_stmt_cache_size = 18446744073709547520

innodb_stats_transient_sample_pages = 8

ft_query_expansion_limit = 20

innodb_compression_level = 6

loose_optimizer_switch = index_merge=on,index_merge_union=on,index_merge_sort_union=on,index_merge_intersection=on,engine_condition_pushdown=on,index_condition_pushdown=on,mrr=on,mrr_cost_based=on,block_nested_loop=on,batched_key_access=off,materialization=on,semijoin=on,loosescan=on,firstmatch=on,subquery_materialization_cost_based=on,use_index_extensions=on

loose_thread_pool_size = 2

delayed_insert_limit = 100

group_concat_max_len = 1024

log_slow_admin_statements = OFF

innodb_flush_neighbors = 1

max_seeks_for_key = 18446744073709500000

innodb_disable_sort_file_cache = ON

innodb_undo_log_truncate = OFF

loose_slave_parallel_workers = 8

loose_inner_schema_list =

log_timestamps = SYSTEM

query_alloc_block_size = 8192

innodb_strict_mode = OFF

delay_key_write = ON

loose_opt_rds_last_error_gtid = ON

avoid_temporal_upgrade = OFF

#rpl_semi_sync_master_wait_point = AFTER_SYNC

innodb_checksum_algorithm = crc32

loose_rds_audit_max_sql_size = 2048

binlog_rows_query_log_events = OFF

key_cache_block_size = 1024

loose_rds_audit_log_buffer_size = 16777216

slow_launch_time = 2

loose_rds_audit_log_reserve_all = OFF

optimizer_search_depth = 62

innodb_autoextend_increment = 64

show_old_temporals = OFF

innodb_commit_concurrency = 0

transaction_alloc_block_size = 8192

innodb_ft_num_word_optimize = 2000

innodb_ft_result_cache_limit = 2000000000

eq_range_index_dive_limit = 10

loose_session_track_transaction_info = OFF

binlog_order_commits = ON

innodb_cmp_per_index_enabled = OFF

slave_parallel_type = LOGICAL_CLOCK

performance_schema = OFF

loose_innodb_adaptive_hash_index_parts = 8

innodb_ft_min_token_size = 3

div_precision_increment = 4

loose_performance_agent_enabled = ON

loose_rds_audit_log_version = MYSQL_V1

explicit_defaults_for_timestamp = OFF

binlog_row_image = full

query_prealloc_size = 8192

innodb_online_alter_log_max_size = 134217728

innodb_optimize_fulltext_only = OFF

loose_innodb_log_optimize_ddl = OFF

loose_rds_audit_log_cached_method = ON

loose_performance_point_enabled = ON

loose_rds_proxy_user_list = aurora_proxy

loose_performance_point_lock_rwlock_enabled = ON

loose_collation_server = utf8_general_ci

default_week_format = 0

innodb_adaptive_flushing = ON

innodb_thread_sleep_delay = 10000

innodb_buffer_pool_dump_pct = 25

loose_rpl_semi_sync_slave_trace_level = 1

connect_timeout = 10

loose_rds_audit_row_limit = 100000

max_sort_length = 1024

tls_version = TLSv1,TLSv1.1,TLSv1.2

default_time_zone = SYSTEM

slave_exec_mode = strict

sql_mode = 'ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'

loose_auto_detect_certs = OFF

innodb_ft_sort_pll_degree = 2

loose_rds_force_myisam_to_innodb = ON

range_alloc_block_size = 4096

innodb_log_compressed_pages = OFF

loose_innodb_encrypt_algorithm = AES_256_CBC

loose_opt_rds_audit_log_enabled = ON

innodb_stats_auto_recalc = ON

log_queries_not_using_indexes = OFF

innodb_status_output = OFF

innodb_compression_pad_pct_max = 50

innodb_adaptive_hash_index = ON

innodb_stats_persistent_sample_pages = 20

innodb_purge_batch_size = 300

query_cache_wlock_invalidate = OFF

innodb_io_capacity_max = 40000

loose_thread_pool_enabled = OFF

innodb_large_prefix = OFF

table_open_cache_instances = 16

transaction_prealloc_size = 4096

innodb_max_undo_log_size = 1073741824

innodb_deadlock_detect = ON

init_connect = ''

loose_rds_user_with_kill_option = rds_jq2_adm_u08


[mysqldump]

quick

max_allowed_packet = 64M


[mysql]

#no-auto-rehash

#prompt = "\\u@\\h : \\d \\R:\\m:\\s> "


[myisamchk]

key_buffer = 512M

sort_buffer_size = 512M

read_buffer = 8M

write_buffer = 8M


[mysqlhotcopy]

interactive-timeout


[mysqld_safe]

user = mysql

basedir = /usr/local/mysql/


[client]

socket = /usr/local/mysql/tmp/mysql.sock


[mysql_install_db]

basedir = /usr/local/mysql/

# include all files from the config directory

!includedir /etc/my.cnf.d
```

