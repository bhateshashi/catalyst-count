#python manage.py test
from django.test import TestCase, Client
from django.contrib.auth.models import User
from django.urls import reverse
from .models import Company

class ViewsTest(TestCase):
    def setUp(self):
        # Create a test user
        self.user = User.objects.create_user(username='testuser', password='testpassword')

        # Create a test company
        self.company = Company.objects.create(name='Test Company', year_founded=2000)

    def test_home_page_view(self):
        client = Client()
        response = client.get(reverse('home'))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'home.html')

    def test_signup_view(self):
        client = Client()
        response = client.post(reverse('signup'), {'username': 'newuser', 'email': 'newuser@example.com', 'password1': 'password123', 'password2': 'password123'})
        self.assertEqual(response.status_code, 302)  # 302 indicates a successful redirect
        self.assertTrue(User.objects.filter(username='newuser').exists())

    def test_login_view(self):
        client = Client()
        response = client.post(reverse('login'), {'username': 'testuser', 'pass': 'testpassword'})
        self.assertEqual(response.status_code, 302)  # 302 indicates a successful redirect
        self.assertTrue(client.session['_auth_user_id'])

    def test_logout_view(self):
        client = Client()
        client.login(username='testuser', password='testpassword')
        response = client.get(reverse('logout'))
        self.assertEqual(response.status_code, 302)  # 302 indicates a successful redirect
        self.assertFalse(client.session.get('_auth_user_id'))

    def test_upload_csv_view(self):
        client = Client()
        client.login(username='testuser', password='testpassword')
        with open('path/to/test.csv', 'rb') as file:
            response = client.post(reverse('upload_csv'), {'csv_file': file})
        self.assertEqual(response.status_code, 200)  # Assuming successful response is 200
        # Add assertions based on the expected behavior of your view

    def test_query_builder_view(self):
        client = Client()
        client.login(username='testuser', password='testpassword')
        response = client.get(reverse('query_builder'))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'query_builder.html')
        # Add assertions based on the expected behavior of your view
