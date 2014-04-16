use strict;
use warnings;
#use utf8;
use 5.10.1;
use File::Spec;
use File::Basename;
use lib File::Spec->catdir(dirname(__FILE__), 'extlib', 'lib', 'perl5');
use lib File::Spec->catdir(dirname(__FILE__), 'lib');
use Amon2::Lite;
use Amon2::Web;
use Digest::MD5 qw/md5_hex/;

our $VERSION = '0.12';

__PACKAGE__->load_plugins(
	'Web::JSON' => { status_code_field => 'status' }
);

get '/' => sub {
	my $c = shift;

	#my $db = $c->db;
	#my $row = $db->select_row( q{select * from books where id=100} );
	
	return $c->render('index.tt');
	#return $c->render_json($row);
};

post '/admin/reset' => sub {
	my $c = shift;
	my $db = $c->db;
	
	my $sth = $db->do(q{TRUNCATE TABLE userid});

	return $c->create_response(204);
	#return $c->render_json({status => 200, message => 'ok'});
};

post '/user/register' => sub {
	my $c = shift;
	my $username = $c->req->param('username');
	
	
	my $password = $c->req->param('password');
	my $api_key = md5_hex($username . $password);	
	
	my $db = $c->db;
	my $sth = $db->do(q{INSERT INTO userid (username, password, apikey) VALUES (?, ?, ?)}, {}, $username, $password, $api_key );
	# $sth->execute("d");
	my $id = $db->last_insert_id('heroku_server', 'heroku_server', 'userid', 'id');
	
	#return $c->render_json($password);
	#return $c->create_response( 200, ['Content-Type' => 'text/plain'], ["id:",$id,", apikey:",$api_key] );
	return $c->create_response( 200, [], ["id:",$id,", apikey:",$api_key] ), $c->render('index.tt');
};

use DBIx::Sunny;
sub db {
    my $self = shift;
    return $self->{db} ||= $self->_db_connect;
}

sub _db_connect {
    my $self = shift;

    my $dbh = DBIx::Sunny->connect("DBI:mysql:heroku_server:heroku_host", "heroku_user" , "heroku_pass", +{
        RaiseError        => 1,
        mysql_enable_utf8 => 1,
    });

    return $dbh;
}


__PACKAGE__->to_app();

__DATA__

