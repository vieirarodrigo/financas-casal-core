# Sistema de Gestão Financeira Compartilhada
Objetivo: Domínio de Next.js, Tipagem Estrita e Segurança na Camada de Dados (RLS).

Arquitetura de Dados:

users - Armazena os usuários do sistema
CREATE TABLE users (
    user_id uuid,
    name text NOT NULL,
    PRIMARY KEY (user_id)
);

groups - Armazena os grupos no qual os usuários podem participar
CREATE TABLE groups (
    group_id uuid,
    PRIMARY KEY (group_id)
);

types_transactions - Armazena os tipos de transações possíveis
CREATE TABLE types_transactions (
    type_id uuid,
    type text NOT NULL,
    PRIMARY KEY (type_id)
);

relationships - Relaciona o usuário ao grupo
CREATE TABLE relationships ( 
    relationship_sk uuid, 
    user_id uuid NOT NULL, 
    group_id uuid NOT NULL, 
    PRIMARY KEY (relationship_sk),
    CONSTRAINT pk_relationship UNIQUE (user_id, group_id), 
    FOREIGN KEY (user_id) REFERENCES users(user_id), 
    FOREIGN KEY (group_id) REFERENCES groups(group_id)
); 

transactions - Armazena todas transações registradas no sistema
CREATE TABLE transactions (
    transaction_id uuid,
    description text,
    date date NOT NULL,
    value integer NOT NULL,
    relationship_sk uuid NOT NULL,
    type_id uuid NOT NULL,
    PRIMARY KEY (transaction_id),
    FOREIGN KEY (relationship_sk) REFERENCES relationships(relationship_sk),
    FOREIGN KEY (type_id) REFERENCES types_transactions(type_id)
);

Triggers:

on_auth_user_created - Será necessário para manter a tabela users atualizada sempre que um usuário se cadastrar em auth:

CREATE TRIGGER on_auth_user_created
AFTER INSERT ON auth.users
FOR EACH ROW
EXECUTE FUNCTION public.new_user();

Funções:

new_user() - Atualiza a tabela users de acordo com usuário cadastrado na tabela auth:

CREATE OR REPLACE FUNCTION public.new_user()
RETURNS TRIGGER
SET search_path = ''
AS $$
BEGIN 
    INSERT INTO public.users (user_id, name)
    VALUES (NEW.id, COALESCE(new.raw_user_meta_data->>'username', new.raw_user_meta_data->>'full_name', 'New User'));
    RETURN NEW;
END;
$$ language plpgsql security definer;

Protocolo de Segurança:

Será usado o JWT para validar/identificar o usuário que acessa o sistema e RLS para garantir a segurança e integridade dos dados, impedindo que terceiro acessem as informações de outros usuários.
