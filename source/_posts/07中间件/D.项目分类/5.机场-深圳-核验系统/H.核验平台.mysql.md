CREATE DATABASE `idszt_test` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';



CREATE TABLE `idszt_test`.`table_idszt_test`  (
  `id` int NOT NULL,
  `name` varchar(255) NULL,
  PRIMARY KEY (`id`)
);


INSERT INTO `idszt_test`.`table_idszt_test` (`id`, `name`) VALUES (1, 'kongkong');
