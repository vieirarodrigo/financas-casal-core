# Sistema de Gestão Financeira Compartilhada
Objetivo: Domínio de Next.js, Tipagem Estrita e Segurança na Camada de Dados (RLS).

Arquitetura de Dados:

Teremos inicialmente 4 tabelas:
Usuário - Armazena os usuários do sistema (userID (pk), nome, data de nascimento, cpf);
Grupo - Armazena os grupos no qual os usuários podem participar (grupoid (pk));
Relacionamentos - Relaciona o usuário ao grupo (relacionamentoid (pk), userid (fk), grupoid (fk);
Transações - Armazena todas transações registradas no sistema (transacaoid (pk), grupoid (fk), autorid (fk), descricao, tipo, data, valor).

Protocolo de Segurança: Explique brevemente como o JWT e o RLS protegerão os dados.
Será usado o JWT para validar/identificar o usuário que acessa o sistema e RLS para garantir a segurança e integridade dos dados, impedindo que terceiro acessem as informações de outros usuários.
